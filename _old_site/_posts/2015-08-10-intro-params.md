---
layout: post
title: params; Simplifying Params
date: 2015-08-10 15:27:31
tags:
- R
- params
- R packages
- cran
---

I have been using R packages to wrangle genomics data for a few years. Often we have a huge list of parameters including paths to apps, their default parameters, reference genome files and other related files. While it is possible to use R's default `options()` function to manage these, often the space becomes quite cluttered. 

Recently, I made a using a small package `params`, which reads configuration files and loads there parameters into R. This attempts to solve a 3 things, 

1. easy reading and loading of parameters [`load_opts()`]
2. dynamic changing of params [`set_opts()`]
3. fetching and pretty printing of params [`get_opts()`]


**`load_opts()`** can automatically recognize a few file extensions, read and load these files:

a. conf/tsv/tab/txt: tab delimited files
b. csv: comma separated
c. xlsx: microsoft excel files (handle with care)

For example, a `.conf` file is assumed to be tab delimited. 

```
conf = system.file("conf/params.conf", package = "params")
opts2 = load_opts(conf)
```

Now if we have a bunch of parameters, its always easier to seem them as a table; `get_opts()` to rescue. This uses the nifty kable function from knitr.

```
get_opts()
|name          |value            |
|:-------------|:----------------|
|default_regex |(.*)             |
|my_conf_path  |~/flowr/conf     |
|my_dir        |path/to/a/folder |
|my_path       |~/flowr          |
|my_tool_exe   |/usr/bin/ls      |
|verbose       |2                |
```
To extract a specific parameter, simply use `get_opts("paramname")`. 
	
Additionally, there are times where we may have a set of nested parameters; i.e. a former parameter is a part of later. This is quite useful, such that one may define the base path for all the tools, and all other paths would be relative to it (`{{{toolpath}}}/samtools`, `{{{toolpath}}}/bwa`, etc.).

Taking a simple example, lets use first and last names.

```
set_opts(first = "John", last = "Doe", ## setting first and last names
    'full_name' = "{{{first}}} {{{last}}}") ## setting full name
```

**Automatic checking**

Additionally, values all parameters ending with `_exe|_path|_dir` are checked by `load_opts`, whether they exist or not. If they don't exist a warning (not error) is thrown, informing user of the issue.

The package is already on [CRAN](https://cran.rstudio.com/web/packages/params/index.html), though a more recent devel version is available on [github](https://github.com/sahilseth/params). Also, a detailed readme and examples are available on the [staticdocs](http://sahilseth.com/params/) website.
