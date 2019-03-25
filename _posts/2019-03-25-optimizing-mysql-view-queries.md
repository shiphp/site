---
layout: post
title: "Optimizing MySQL View Queries"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,mysql]
---

Last year I started logging slow requests using PHP-FPM's [slow request log](https://easyengine.io/tutorials/php/fpm-slow-log). This tool provides a very helpful, high level view of which requests to your website are not performing well, and it can help you find bugs, memory leaks, and optimizations for your database queries.

After a few weeks, I noticed that two requests were especially slow - consistently taking 3-5 seconds per request. While this request was not very common (and only used by internal company employees), it still indicated an area for improvement in our codebase.

## The Root Problem

In order to track down why the request was slow, I installed [Datadog](https://www.datadoghq.com/), which offers more detailed, query-level data on how requests are performing. This was hugely valuable for tracking down the root of the problem.

After watching queries for about a week, two database queries topped my list for the slowest in our system:

![Our slowest database queries were to two related MySQL views](https://i.imgur.com/RfKMaL4.png)

Both of these `select` statements are querying two related MySQL views. The `school_summaries` view uses the `teacher_summaries` view to show internal team members how many assignments teachers at each school have posted, how many they've reviewed, and a bunch of other disparate information.

## Why Use Views?

Let's back up because a lot of people argue that [MySQL Views shouldn't be used due to performance issues](https://www.percona.com/blog/2007/08/12/mysql-view-as-performance-troublemaker/). This is naive; like any tool in a programmer's toolbelt, views have a place, but must be used with knowledge of the tradeoffs.

Our application ([The Graide Network](https://www.thegraidenetwork.com/)) is a grading platform where we grade essays for teachers. We have several database tables that contain information about how teachers use our platform, and we must check this usage from time to time to generate reports for school leaders who pay for our services.

Some of the information we track about teachers includes:

- Email
- School name
- Last login date
- Number of assignments posted
- Number of assignments reviewed
- Number of assignments the teacher has requested a revision for
- Number of assignments completed
- Number of hours used (schools typically pay for packages of "hours")
- Most recent assignment posted date
- Reviews they've given us

As you can imagine, this data is stored in various tables throughout our database, so in order to query it, we would need to join lots of data in a single large query. This query ends up looking like this:

```mysql
select
  users.ID as teacher_id,
  users.email,
  users.first_name,
  users.last_name,
  users.school,
  users.login_on,
  users.created_on,
  t1.teacher_revision_request_count,
  t2.admin_revision_request_count,
  t3.section_assignments_awaiting_review,
  transaction_complete.total as total_assignments_complete,
  transaction_started.total as total_assignments_posted,
  transaction_complete.section_count as total_section_assignments_complete,
  transaction_started.section_count as total_section_assignments_posted,
  transaction_started.hours_used as total_hours_used,
  transaction_started.most_recent as most_recent_assignment_posted_date,
  avg(NULLIF(reviews.accuracy, 0)) as average_accuracy_review_given,
  avg(NULLIF(reviews.quality, 0)) as average_quality_review_given
from users
left join (
    select *
    from graider_reviews
    WHERE (
        (MONTH(CURDATE()) <= 7 AND created_at >= DATE_SUB(DATE_FORMAT(CURDATE(), '%Y-07-01'), INTERVAL 1 YEAR))
        OR
        (MONTH(CURDATE()) > 7 AND created_at >= DATE_FORMAT(CURDATE(), '%Y-07-01'))
    )
) as reviews on users.ID = reviews.teacher_id
left join (
    select
      sum(transactions.hours) as hours_used,
      max(transactions.created_at) as most_recent,
      count(transactions.id) as total,
      sum(transactions.number_of_sections) as section_count,
      teacher_id
    from transactions
    where type LIKE 'DEBIT'
    group by teacher_id
) as transaction_started on users.ID = transaction_started.teacher_id
left join (
    select
      count(transactions.id) as total,
      sum(transactions.number_of_sections) as section_count,
      teacher_id
    from transactions
    where (type LIKE 'DEBIT')
    group by teacher_id
) as transaction_complete on users.ID = transaction_complete.teacher_id
left join (
  select a.teacher_id, count(sa.id) as teacher_revision_request_count from section_assignments as sa
  join assignments a on sa.assignment_id = a.id
  where sa.teacher_revision_requested_at is not null
  and sa.deleted_at is null
  group by a.teacher_id
 ) t1 on users.ID = t1.teacher_id
left join (
  select a.teacher_id, count(sa.id) as admin_revision_request_count from section_assignments as sa
  join assignments a on sa.assignment_id = a.id
  where sa.admin_revision_requested_at is not null
  and sa.deleted_at is null
  group by a.teacher_id
) t2 on users.ID = t2.teacher_id
left join (
  select a.teacher_id, count(sa.id) as section_assignments_awaiting_review from section_assignments as sa
  join assignments a on sa.assignment_id = a.id
  where sa.status = 'Awaiting Review'
  and sa.deleted_at is null
  group by a.teacher_id
) t3 on users.ID = t3.teacher_id
where users.type = 't'
and users.school is not null
group by teacher_id, email, first_name, last_name, school, login_on, created_on,
         total_assignments_complete, total_assignments_posted,
         total_section_assignments_complete, total_section_assignments_posted,
         total_hours_used, most_recent_assignment_posted_date;
```

I realize there is a lot to unpack in that query, but that's sort of the point. Making this query, modifying, and improving it would be darn near impossible using our ORM (we use [Laravel's Eloquent](https://laravel.com/docs/master/eloquent)), and if we wrote a raw query, it would be really hard to add new `where`, `order by`, or `limit` clauses as we'd have to insert them manually.

Creating a MySQL view gives you the best of both worlds though. Once it's set up, you can [wrap it in a Laravel model](https://stitcher.io/blog/eloquent-mysql-views) and query it using the Eloquent ORM. This means that I can write simple code to query that huge, ugly mess of SQL above:

```php
<?php
// Get the first 10 teachers at "A High School"
TeacherSummary::where('school', 'A High School')->skip(0)->take(10)->get();
```

## Benchmarking the View

I really like this pattern, but as you can imagine it comes with some tradeoffs. The biggest is that performance is opaque. It's really hard to debug what's slow in a view because the whole thing ends up as a single query. To get a high level benchmark for how the query was performing, I just ran a query to the view from an SSH connection:

```mysql
select * from teacher_sumaries;
```

My IDE told me execution took 1.184 seconds. For comparison, making the same `select *` statement from our `users` table took 0.236 seconds. This table and view are on the same order of magnitude from a size perspective, so ideally they would run at similar speeds.

## Breaking Down the Slow Parts of the View

Because MySQL views are just a shortcut to the underlying query, I decided to start by running an [`explain`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html) on the `select * from teacher_summaries;` query. This gave me the following output:

|id |rows     |filtered|Extra                                                              |
|---|---------|--------|-------------------------------------------------------------------|
|1  |61833440359|100     |                                                                   |
|2  |1117     |17.44   |Using where; Using temporary; Using filesort                       |
|2  |4330     |100     |Using where; Using join buffer (Block Nested Loop)                 |
|2  |10       |100     |Using where                                                        |
|2  |10       |100     |Using where                                                        |
|2  |10       |100     |                                                                   |
|2  |10       |100     |                                                                   |
|2  |7        |100     |                                                                   |
|3  |721      |10      |Using where; Using temporary; Using filesort                       |
|3  |1        |100     |                                                                   |
|4  |5481     |9       |Using where; Using temporary; Using filesort                       |
|4  |1        |100     |                                                                   |
|5  |5481     |9       |Using where; Using temporary; Using filesort                       |
|5  |1        |100     |                                                                   |
|6  |2074     |51.74   |Using where                                                        |
|7  |2074     |51.74   |Using where                                                        |

I like `explain`, but this was too much. I see there's a problem in the first row of output (MySQL is scanning 61,833,440,359 rows just to execute a `select *`!), but I have no idea which row corresponds to which part of the query.

So, I started breaking down the query into smaller bits. I took the `select` statement for each `join`'s subquery and ran an `explain` on it. For example, this subquery's `explain`:

```mysql
explain select a.teacher_id, count(sa.id) as teacher_revision_request_count from section_assignments as sa
  join assignments a on sa.assignment_id = a.id
  where sa.teacher_revision_requested_at is not null
  and sa.deleted_at is null
  group by a.teacher_id;
```

Netted this result:

|id |rows     |filtered|Extra                                                              |
|---|---------|--------|-------------------------------------------------------------------|
|1  |5481     |9       |Using where; Using temporary; Using filesort                       |
|1  |1        |100     |                                                                   |

Now I'm getting somewhere! This output tells me that the query must go through 5481 rows of the `section_assignments` table in order to execute. That's almost all the rows in the table!

If this subquery were the only thing running, performance wouldn't be that bad - MySQL can query ~6k rows quickly with ease - but problematic subquery multiplies the number of rows that must be loaded, which is why the view was loading so many rows.

## Improving the View

I continued this process with each subquery to figure out which ones were loading thousands of rows. I wasn't looking to make this view execute as fast as humanly possible - it's still a very infrequently accessed query - but I did want to take execution time down by an order of magnitude. 

I looked at the indexes in the `section_assignments` table and realized I had overlooked indexing the `teacher_revision_requested_at`, `admin_revision_requested_at`, and `deleted_at` columns. As a general rule of thumb, I index date columns as they're often used in queries, but when these columns were created our database was small and we were moving fast, so I missed them. I had similarly missed indexing the `graider_reviews.teacher_id` column, so the `join` was scanning all rows in that table as well.

Another part of the query that was especially problematic was the subquery that joins in `DEBIT` transactions:

```mysql
select
  count(transactions.id) as total,
  sum(transactions.number_of_sections) as section_count,
  teacher_id
from transactions
where (type like 'DEBIT')
group by teacher_id;
```

The indexes were good (`teacher_id` and `type` were both being indexed), but [changing `like` to `=`](https://stackoverflow.com/questions/6142235/sql-like-vs-performance) I doubled the performance (halved the number of rows scanned) for this subquery.

I created a temporary duplicate of our production database, tested out my new indexes, and measured their results to see how these improvements affected performance overall.

## Final Results

After pushing my new indexes to production, I saw a huge improvement in performance. First, by running the `explain select * from teacher_summaries;`, I saw that _only_ 215 million rows were being scanned. This isn't great, but compared with 62 billion before, the effect was noticeable. Here are the full results: 

|id |rows     |filtered|Extra                                                              |
|---|---------|--------|-------------------------------------------------------------------|
|1  |215652711|100     |                                                                   |
|2  |1117     |14.99   |Using where; Using temporary; Using filesort                       |
|2  |11       |100     |Using where                                                        |
|2  |10       |100     |Using where                                                        |
|2  |10       |100     |Using where                                                        |
|2  |10       |100     |                                                                   |
|2  |10       |100     |                                                                   |
|2  |10       |100     |                                                                   |
|3  |865      |85.28   |Using where; Using temporary; Using filesort                       |
|3  |1        |100     |                                                                   |
|4  |394      |85.28   |Using index condition; Using where; Using temporary; Using filesort|
|4  |1        |100     |                                                                   |
|5  |183      |85.28   |Using index condition; Using where; Using temporary; Using filesort|
|5  |1        |100     |                                                                   |
|6  |1105     |100     |Using index condition; Using temporary; Using filesort             |

You can also see that several parts of the query that were previously scanning ~5k rows are now down to around 1k rows. This is the primary performance improvement for this query.

Ultimately, rows scanned doesn't matter that much to me though (I'm on managed database hosting and hardware is cheap). What matters is speed for users, and that improved most dramatically. Before the indexes, it took about 1.2 seconds, and now it was taking 0.3 seconds to perform the `select *` query on the view.

## Conclusion

While there is definitely more I could do to improve performance of this view, I'm okay with the gains I've made with these simple fixes. Software engineering is a constant balance of performance vs. pragmatism, and at our scale, we have to understand that. I'd welcome other suggestions for improving the performance of MySQL views, so [reach out to me on Twitter](https://www.twitter.com/shiphpnow) if you have your own story to add.
