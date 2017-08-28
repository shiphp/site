# Shiphp Landing Page and Blog

This hosts the landing page and blog at [www.shiphp.com](https://www.shiphp.com/).

## Dev Notes

- Build the site: `docker run --rm -it -v $PWD:/srv/jekyll jekyll/jekyll:latest jekyll build`
- Serve locally: `docker run --rm -it -v $PWD:/srv/jekyll -p 4000:4000 jekyll/jekyll:latest jekyll serve`
