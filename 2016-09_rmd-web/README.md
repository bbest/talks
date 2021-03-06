# Notes for Demo

## Intro

- car permit
- community effort. no snacks. leave early ok
- stickies for ideas. present?: email
- intro presenters
- record screencast

## Git vs Github

* [GitHub Pages](https://pages.github.com/): websites for you and your projects, hosted directly from your GitHub repository. Just edit, push, and your changes are live

## Demo

- preliminaries: <http://pages.github.io>, RStudio preview release

- new Github group sb-r-users. billing email.

- new Github repo sb-r-users.github.io. init w/ README.

  - Edit, add URL sb-r-test.github.io

  - Clone, copy to clipboard https://github.com/sb-r-test/sb-r-test.github.io.git

- RStudio, New Project https://github.com/sb-r-test/sb-r-test.github.io.git
  - Git
  
- [R Markdown Websites - simple example](http://rmarkdown.rstudio.com/rmarkdown_websites.html#simple_example)

  - File > New File > text: `_site.yml`

  - File > New File > Rmarkdown...: `index.Rmd`, `about.Rmd`

- render

  ```r
  rmarkdown::render_site()
  ```

- ok, but to `_site`. <http://rmarkdown.rstudio.com/rmarkdown_websites.html#publishing_websites>

  1. Add a file named .nojekyll to your site source code directory (this tells GitHub Pages to not process your site with the Jekyll engine).

  1. Change your output directory to “.” within _site.yml. For example:

  ```yaml
output_dir: "."
```

- Build?

  - reopen, Build > Build Website
  
  - delete `_site` folder

- OK, ready to check in files in Git pane: explain files...

```
.gitignore
.nojekyll
README.html
README.md
_site.yml
about.Rmd
about.html
index.Rmd
index.html
sb-r-test.github.io.Rproj
site_libs
```
- commit and push. view website.

- add some data-driven content

## Table

See vignette

```r
library(DT)
datatable(iris)
```

## Plot

See vignette

```r
library(plotly)
m <- loess(unemploy / pop ~ as.numeric(date), data = economics)
p <- plot_ly(economics, x = date, y = unemploy / pop, name = "raw") 
add_trace(p, x = date, y = fitted(m), name = "loess")
```

## Map

See `?leaflet`

```r
library(leaflet)

leaflet() %>% 
  addTiles() %>% 
  setView(-119.845559, 34.4136876, zoom = 15)
```

- `echo=F` or 

```yaml
output:
  html_document:
    toc: true
    toc_float: true
    number_sections: true
    code_folding: hide
```

- `message=F` to suppress plotly loading messages

- change theme

- more at [htmlwidgets](http://www.htmlwidgets.org/showcase_leaflet.html)

- website 

- table: [R for websites & data science, useR meeting highlights - Santa Barbara R Users Group (Santa Barbara, CA) | Meetup](http://www.meetup.com/Santa-Barbara-R-Users-Group/events/233716974/) -> Tools: download attendeed list

- add leaflet map

- Note Help > About: Version 1.0.20 = [Download RStudio Preview](https://www.rstudio.com/products/rstudio/download/preview/) vs 0.99.903 = [Download RStudio](https://www.rstudio.com/products/rstudio/download/)


## Continuous Integration

- see [autogenerate rmd to html on marinebon.github.io (using Travis-CI) · Issue #21 · marinebon/private](https://github.com/marinebon/private/issues/21)

Simply edit markdown (even with web browser on Github), and site will rebuild (within 10 min).

* [Bookdown: Authoring Books with R Markdown](https://bookdown.org/yihui/bookdown/github.html)
* [R on Travis-CI | RStudio Blog](https://blog.rstudio.org/2016/03/09/r-on-travis-ci/)

steps to get travis to push updated github html files:

- create `.travis.yml`

```yaml

```

- Since this Travis service is primarily for checking R packages, you will also need a (fake) DESCRIPTION file as if the book repository were an R package.

- create personal access token in Github w/ repo privileges
- 

- [R on Travis-CI | RStudio Blog](https://blog.rstudio.org/2016/03/09/r-on-travis-ci/)

```bash
GH_OWNER=marinebon
GH_REPO=marinebon.github.io
GH_BRANCH=master
GH_TOKEN=86a38df63e8f344c4c595d3e76a060f0dff3FAKE
DIR=~/github

GH_SLUG=$GH_OWNER/$GH_REPO

cd $DIR/$GH_REPO

travis encrypt -r $GH_SLUG GH_TOKEN=$GH_TOKEN --add env.global
travis enable -r $GH_SLUG

git commit -am "enabled travis.yml with encrypted github token"
git pull
git push
```

view `~/.github-token_marinebon`

- add repo permissions, per [Travis CI's use of GitHub API Scopes - Travis CI](https://docs.travis-ci.com/user/github-oauth-scopes/)

deploy:

- [happy-git-with-r/_deploy.sh at master · jennybc/happy-git-with-r](https://github.com/jennybc/happy-git-with-r/blob/master/_deploy.sh)
- [courses/deploy.sh at master · ccfd/courses](https://github.com/ccfd/courses/blob/master/tools/deploy.sh)


- [skip ci]
- add badge
  - [Embedding Status Images - Travis CI](https://docs.travis-ci.com/user/status-images/)




