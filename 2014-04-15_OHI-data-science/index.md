data science for the ocean health index
====
author: ben best <bbest@nceas.ucsb.edu>
date: 2014-04-15 in santa barbara, ca

overview
====
type: prompt
incremental: true

1. ocean health index
1. data flow
1. data wrangling
1. distributed development
    
what is a healthy ocean?
====
incremental: true

* pristine?

  ![what is a healthy ocean?](./img/healthy_ocean.png)

* "a healthy ocean _sustainably_ delivers a range of _benefits_ to _**people**_ _now_ and in the _future_"

*** 

* quantify using publicly available datasets (_mostly_)
* halpern et al. (2012) _nature_.
* rolling out toolbox for individual countries to formulate own index
* "reproducible research"

what do we measure?
====

### goals
1. natural products
1. food provision
1. artisanal fishing
1. carbon storage
1. coastal protection
1. tourism & recreation
1. livelihoods & economies
1. sense of place
1. biodiversity
1. clean waters

***
### process
* set a reference point
* index = avg(status, future)
* future ~ status, trend, resilience (+), pressures (-)
* pressure = matrix of weights, goals vs pressures
* resilience = matrix of weights, goals vs resilience

overview
====
type: prompt

1. ocean health index
1. **data flow**
1. data wrangling
1. distributed development

flow
====
incremental: true

* darren hardy ([https://github.com/drh-stanford](github.com/drh-stanford))
* [**linear-flow**](https://github.com/drh-stanford/linear-flow): _swiss-army knife for scientific workflows_
* linux file server with ssd, postgresql 
* data/ 
  - raw/ -> ingest/ -> stable/ -> model/
     + [study area]-[provider]-[product]_[version]/ <br>
       eg "GL-WorldBank-Statistics_v2012/"
         * manual_output/, tmp/ -> data/
         * scripts.*: setup, download, import, ingest, model, run, digest, export, report, upload, finish
         * *.languages: sh, py, r, sql, pl, rb
flow: install & run
====
incremental: true

```bash
python setup.py install
cd example
flow
```

```
Running flow [rootdir=~/linear-flow-master/example] depth=0 nproc=1

Running package with default style [~/linear-flow-master/example]

Running python script import.py
Generating some dummy data in data.csv...

Running R script model.R
Running R script model.r
```

flow: principles
====
incremental: true

* extract, transform, load (etl)
* makefile
* common output format: data/\*.**csv**

overview
====
type: prompt

1. ocean health index
1. data flow
1. **data wrangling**
1. distributed development

data wrangling
====
incremental: true

* **task**: calculate the batting average (AVG): number of base hits (H) divided by the total number of at bats (AB) using the Lahman baseball database. limit to Babe Ruth and Jackie Robinson.

* setup


```r
library(Lahman)
library(dplyr)
library(RSQLite)
```


* result

```
  nameFirst nameLast    avg
      Babe      Ruth  0.323
    Jackie  Robinson  0.308
```

data wrangling: sql
====

* **sql** ([**csv2psql**](https://github.com/drh-stanford/csv2psql))


```r
tbl(lahman_sqlite(), sql(
"SELECT nameFirst, nameLast, 
  ROUND(AVG(H/(AB*1.0)), 3) AS avg 
FROM Batting
JOIN Master USING (playerID)
WHERE AB > 0 AND ((
  (nameFirst = 'Babe' AND 
   nameLast = 'Ruth') OR 
  (nameFirst = 'Jackie' AND 
   nameLast = 'Robinson')) 
GROUP BY nameFirst, nameLast
ORDER BY avg DESC")))
```


data wrangling: dplyr 
====

* chaining (`%.%`): grammar of data manipulation


```r
Batting %.%
  merge(Master, by='playerID') %.%
  filter(
    AB > 0 &
    (nameFirst=='Babe' & 
     nameLast =='Ruth') | 
    (nameFirst=='Jackie' & 
     nameLast =='Robinson')) %.%  
  group_by(nameFirst, nameLast) %.%
  summarise(avg = round(mean(H/AB), 3)) %.%
  arrange(desc(avg))
```


overview
====
type: prompt

1. ocean health index
1. data flow
1. data wrangling
1. **distributed development**


distributed dev: fork & pull
====

| direction | org web                  | user web                   | user local          |
| --------- | -----------------------  | -------------------------- | ------------------- |
|           | `github.com/[org]/[repo]`| `github.com/[user]/[repo]` |   `~/github/[repo]` |
| -> (1x)   |                          | -> [fork](https://help.github.com/articles/fork-a-repo)  | -> [clone](https://help.github.com/articles/fetching-a-remote)    |
| <- |  [merge](https://help.github.com/articles/merging-a-pull-request) {admin} <- | <- [pull request](https://help.github.com/articles/creating-a-pull-request) | <- [push](https://github.com/OHI-Science/ohiprep/wiki/Setup#rstudio), <-> [commit](https://github.com/OHI-Science/ohiprep/wiki/Setup#rstudio) |

where:
* `[org]` is an organization (eg `ohi-science`)
* `[repo]` is a repository in the orgranization (eg `ohiprep`)
* `[user]` is your github username (eg `bbest`)

github features
====
incremental: true

* track changes, issues, etc. free for public repos
* max: 1GB per repo, 100MB per file. so larger files (and binary) on file server, with remote vpn option
* render markdown, eg README.md
* [csv view](https://github.com/OHI-Science/ohiprep/wiki/Software-Features#csv-view)
* [geojson view](https://github.com/OHI-Science/ohiprep/wiki/Software-Features#geographic-data-view)

for more...
====
type: sub-section
* [ohi-science.org](http://ohi-science.org)
  - ohiprep: data layer preparation
  - ohicore: r library of core functions
  - ohigui: graphical user interface with shiny and rCharts
* [github.com/bbest](github.com/bbest) / bbest.github.com / talks
  - [this talk](http://bbest.github.io/talks/2014-04-03_R-productivity), as [r presentation](http://bbest.github.io/talks/2014-04-03_R-productivity/index.Rpres)
  - [productivity with rstudio](http://bbest.github.io/talks/2014-04-03_R-productivity/): 1. data wrangling with dplyr, 2. documenting with markdown, 3. versioning with github
* other
  - [marwick reproducible research](http://benmarwick.github.io/CSSS-Primer-Reproducible-Research)
  - [github.com/datasets](http://data.okfn.org/roadmap/core-datasets)
