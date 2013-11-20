title: PHP and Solr
author:
  name: Lorenzo Fontana
  twitter: fontanalorenz
  email: fontanalorenzo@me.com
output: php-and-solr.html

--
<p align="center" style="padding-top:100px">![PHP and Solr](phpandsolr.png)</p>
## They love to work toghether!

--
###Who am I
- Lorenzo Fontana
- Developer at Facile.it
- 20 years old
- [github.com/fontanalorenzo](http://github.com/fontanalorenzo)
- [twitter.com/fontanalorenz](http://twitter.com/fontanalorenz)

--
### What's included
- The slides, in the *slides* folder
- The Vagrant machine in the *vagrant* folder (we will **up** during this talk)
- The slides are generated from markdown using [cleaver](https://github.com/jdan/cleaver)
--
###What is Solr?
- Solr is a standalone **enterprise search server** with a REST-like API. You put documents in it (called "indexing") via XML, JSON, CSV or binary over HTTP. You query it via HTTP GET and receive XML, JSON, CSV or binary results.
- Solr is based on **Apache Lucene** and it comes from the **Apache Software Foundation**.

--
###Key features
<ul style="font-size: 18px">
    <li>Full-Text Search Capabilities</li>
    <li>Comprehensive HTML Administration Interfaces</li>
    <li>Near Real-time indexing</li>
    <li>Highly Scalable Distributed search with sharded index across multiple hosts</li>
    <li>Faceted Search and Filtering</li>
    <li>Rich Document Parsing and Indexing (PDF, Word, HTML, etc)</li>
    <li>Multiple search indices</li>
    <li>HTTP interface with configurable response formats (XML/XSLT, JSON, Python, Ruby, PHP, Velocity, CSV, binary)</li>
    <li>Sort by any number of fields, and by complex functions of numeric fields</li>
    <li>Search Suggestions and Autocomplete</li>
    <li>Spatial Search</li>
    <li>Dynamic fields</li>
</ul>
--

### Who use it?

- Packagist
- Cloudera
- Digg
- AOL
- SourceForge
- WhiteHouse.gov
- Others..

--
### Why should I use it?
- Application agnostic search logic;
- Solr text search is faster and more sophisticated than text search on a relational DB.
- Scalability across multiple servers ([shard](https://cwiki.apache.org/confluence/display/solr/Distributed+Search+with+Index+Sharding) or [replica](https://cwiki.apache.org/confluence/display/solr/Index+Replication) )
- Schema-less (can add new fields on-the-fly with dynamicfields
- Vibrant and active community
- Indexing of binary documents such as Word documents and PDFs
- Is a mature project

--

### Why should I use it with PHP?
- There's a good and updated library named Solarium that allows PHP applications to speak with Solr (we will speak about it later);
- No worry about writing and managing complex search logic in huge php files;
- Facets, without it are hard to implement

--
#Getting Started
We will take a look on how to deploy a simple Solr instance using the **bundled examples**

--
###System Requirements
1. JRE/JDK **1.6** or Later
2. **A servlet container** such as Tomcat, Jetty, or Resin (I prefer Jetty,
a Jetty Servlet container is also bundled in the examples directory)


--
###Download
1. Download Solr from one of its mirrors.
<pre>http://lucene.apache.org/solr/mirrors-solr-latest-redir.html</pre>
2. Untar it
<pre>tar -xvf solr-4.5.1.tgz</pre>
3. cd into the examples folder
<pre>cd solr-nightly/example/</pre>

--
###Try it
Solr can run in **any Java Servlet container**, in this example we will
use a bundled Jetty servlet container.
1. Start Solr
<pre>java -jar start.jar</pre>
2. Go to
<pre>http://yourhost:8983/solr</pre>
3. Your Solr instance is ready
--
<p align="center">
![Me gusta](me-gusta.png)
</p>
--
Ok, thank you but now, what should I do with my new Solr instance?
--
###Create a new Core
<pre>cd downloaded-solr/example/solr</pre>
This is how the directory should look like

    -rw-r--r--@ 1 fontanalorenzo  staff   2.4K 12 Sep 18:10 README.txt
    drwxr-xr-x@ 2 fontanalorenzo  staff    68B 12 Sep 18:10 bin
    drwxr-xr-x@ 5 fontanalorenzo  staff   170B 12 Sep 18:10 collection1
    -rw-r--r--@ 1 fontanalorenzo  staff   1.7K 12 Sep 18:10 solr.xml
    -rw-r--r--@ 1 fontanalorenzo  staff   501B 12 Sep 18:10 zoo.cfg

copy the default core to make your own

    cp collection1 my-core
    cd my-core
--
### core.properties
Edit the core.properties file changing the core name and telling
Solr to spend all the time necessary to fully load the cores on startup.
A list of possible cases can be found [here.](http://wiki.apache.org/solr/LotsOfCores)

    vim core.properties

    name=my-core
    loadOnStartup=true
    transient=false

--
###The Conf folder
As the name suggest, the conf folder contains the configuration files.
Fortunatelly (or unfortunatelly ? ) they are **just xml files.**

**The most important are:**
- schema.xml
- solrconfig.xml

--
###schema.xml [example](files/schema.xml)
#####Solr stores given pieces of raw data into one or more fields in its index
The **schema.xml** file contains all of the details about which fields your documents can contain,
and how those fields should be dealt with when adding documents to the index, or when querying those fields.

**It can contain:** fields, copyfields, dynamicfields and fields types


--
###solrconfig.xml [example](files/solrconfig.xml)
**solrconfig.xml** is the file that contains most of the parameters for configuring Solr itself.
One of the most important configuration in this file is the **DIH** (data import handler)  configuration

    <requestHandler name="/dataimport"
                    class="solr.handler.dataimport.DataImportHandler">
        <lst name="defaults">
            <str name="config">my_database.xml</str>
        </lst>
    </requestHandler>

See the [my_database.xml](files/my_database.xml) example.
--
###Sitography
- [http://en.wikipedia.org/wiki/Apache_Solr](http://en.wikipedia.org/wiki/Apache_Solr)
- [http://lucene.apache.org/solr/](http://lucene.apache.org/solr/)