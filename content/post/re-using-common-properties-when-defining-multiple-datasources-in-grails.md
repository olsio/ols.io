+++
date = "2014-01-21T12:00:00"
draft = "false"
title = "Re-using common properties when defining multiple datasources in Grails"
slug = "re-using-common-properties-when-defining-multiple-datasources-in-grails"

+++

## tl;dr:
Avoiding code duplication when defining multiple datasources in Grails.

## Long version

One of my [Grails](http://grails.org/) applications has to connect to seven database instances that share many characteristics like driver, dialect, username and password which were all copy pasted in my first version.

Example:
<script src="https://gist.github.com/olsio/e12bef54855ba6a5a135.js"></script>

As you can see only the **url** parameter changes while the rest stays static and is wasted text.

## Shorter version
To make it more compact and re-use the common part I found that you can define a closure which can be used to initialise each datasource entry.

<script src="https://gist.github.com/olsio/aef992eb8fa2af66a812.js"></script>

As you can see I also included the **url** which I will override after the initialisation.

Using the closure this leaves us with the following version.

<script src="https://gist.github.com/olsio/952f5f68ec03e2e5f27a.js"></script>

The final version can be download [here](https://gist.github.com/olsio/9a897f0a4d779a9a18cf/raw/e00508b1d2a304dba28a4fe40ce9b52b276e1376/DataSource.groovy).