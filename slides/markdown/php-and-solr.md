title: PHP and Solr
author:
  name: Lorenzo Fontana
  twitter: fontanalorenz
  email: fontanalorenzo@me.com
  theme: jdan/cleaver-retro
  agenda: true
output: ../php-and-solr.html

--
<p align="center" style="padding-top:100px">![PHP and Solr](img/phpandsolr.png)</p>
## They love to work toghether!

--
###Who am I
<p style="float:left; margin-right: 30px">![PHP and Solr](img/renzo.jpg)</p>
- Lorenzo Fontana
- Developer at Facile.it
- [github.com/fontanalorenzo](http://github.com/fontanalorenzo)
- [twitter.com/fontanalorenz](http://twitter.com/fontanalorenz)

--
### What's included
- The slides, in the **slides** folder
- The Vagrant machine in the **vagrant** folder (we will **up** it during this talk)
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
- It's a mature project

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
![Me gusta](img/me-gusta.png)
</p>
--
#Ok, thank you
##but now, what should I do with my new Solr instance?
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

--
###my_database.xml [example](files/my_database.xml)
**my_database.xml** is the config file we previously declared in the **solrconfig.xml**,
this file configure how to take data and how to map them with fields of the document.
In our case, data are coming from a mysql database, and to retrieve data we have to do some entity queries,
each entity query refers to the query that is used when doing a full import.
--
####From the documentation:
1. The **query** gives the data needed to populate fields of the Solr document in full-import
2. The **deltaImportQuery** gives the data needed to populate fields when running a delta-import
3. The **deltaQuery** gives the primary keys of the current entity which have changes since the last index time
--
#Now lets see some PHP!
--
###Solarium: Intro
Solarium is the most known PHP (5.3 +) **Solr Client**, a full list of its features
can be found [here](http://www.solarium-project.org/why-solarium/features/).
The features that I appreciate more than others are
- Facet support
- Query building
- MoreLikeThis
- Spellcheck
--
### Solarium: Installation
In composer.json add

    {
      "require": {
          "solarium/solarium": "3.1.*"
      }
    }

And then

    composer update
--
### Solarium: Solr endpoint config
    $config = array(
        'endpoint' => array(
            'localhost' => array(
                'host' => 'yourhost,
                'port' => 8983,
                'path' => '/solr/',
            )
        )
    );
--
### Solarium: A select query example
    /* create a client instance */
    $client = new Solarium_Client($config);

    /* get a select query instance */
    $query = $client->createSelect();

    /* Set a query (all documents where name is renzo and email contains @gmail.com) */
    $query->setQuery('name:renzo AND email:@gmail.com');

    /* set start and rows param (comparable to SQL limit) using fluent interface */
    $query->setStart(1)->setRows(20);

    /* set fields to fetch (this overrides the default setting 'all fields') */
    $query->setFields(array('id','name','surname', 'email'));

    /* sort the results by id DESC */
    $query->addSort('id', Solarium_Query_Select::SORT_DESC);

    /* this executes the query and returns the result */
    $resultset = $client->select($query);

    /* total number of results found */
    $numFound = $resultset->getNumFound();


--
### Solarium: An example with facets
    // create a client instance
    $client = new Solarium\Client($config);

    // get a select query instance
    $query = $client->createSelect();

    // get the facetset component
    $facetSet = $query->getFacetSet();

    // create a facet field instance and set options
    $facetSet->createFacetField('type')->setField('type');

    // this executes the query and returns the result
    $resultset = $client->select($query);

    // display facet counts
    echo '<hr/>Facet counts for field "type":<br/>';
    $facet = $resultset->getFacetSet()->getFacet('stock');
    foreach($facet as $value => $count) {
        echo "{$value} [{$count}]<br/>"; // ex output: type name [100]
    }
--
#Framework integration
##What happens if I'm on a **ZF2** or **Symfony 2** Application?

--
###ZF2 Integration
There's a ZF2 module built around Solarium that allows
you to connect Solr and your ZF2 application without headache.

The Ewgo/SolariumModule can be found here [https://github.com/Ewgo/SolariumModule](https://github.com/Ewgo/SolariumModule)

It integrates with **Zend Developer Tools** and has an **adapter for Zend\Paginator**
--
###ZF2 Integration: Installation
In composer.json add

    {
      "require": {
          "ewgo/solarium-module": "1.0.2"
      }
    }

and then

    composer update

--
###ZF2 Integration: Enable the module

In your **application.config.php** add the module

    'modules' => array(
        //...
        'SolariumModule'
    ),

--
###ZF2 Integration: Configuration

Configure your Solr endpoint

    array(
        'solarium' => array(
            'endpoint' => array(
                'default' => array(
                    'host' => 'yourhost',
                    'port' => 8983,
                    'path' => '/solr',
                    'core' => 'default',
                    'timeout' => 5
                )
            )
        )
    )

--
###ZF2 Integration: Usage
    $client = $serviceLocator->get('solarium');

    // From here on, it's all the same as solarium
    $query = $client->createSelect();
    $resultset = $client->execute($query);
--
###ZF2 Integration: Paginate results
This example shows how to use the built-in **Zend\Paginator adapter**

    use \EwgoSolarium\Paginator\Adapter\SolariumPaginator;

    $paginator = new \Zend\Paginator\Paginator(
        new SolariumPaginator($client, $query)
    );
    $paginator->setCurrentPageNumber($page);
    $paginator->setItemCountPerPage($countPerPage);

--
###Symfony 2 Integration
On the other hand, for Symfony 2 there's a bundle from Nelmio named **NelmioSolariumBundle**.

The **NelmioSolariumBundle** can be found here [https://github.com/nelmio/NelmioSolariumBundle](https://github.com/nelmio/NelmioSolariumBundle)
--
###Symfony 2 Integration: Installation
In composer.json add

    {
        "require": {
            "nelmio/solarium-bundle": "2.*"
        }
    }

and then

    composer update

--
###Symfony 2 Integration: Enable the bundle

    $bundles = array(
        ...
        new Nelmio\SolariumBundle\NelmioSolariumBundle(),
        ...
    );
--
###Symfony 2 Integration: Configuration
Configure your Solr endpoint

    nelmio_solarium:
        endpoints:
            default:
                host: yourhost
                port: 8983
                path: /solr
                core: default
                timeout: 5
        clients:
            default:
                endpoints: [default]

--
###Symfony 2 Integration: Usage
    $client = $this->get('solarium.client');

    // From here on, it's all the same as solarium
    $select = $client->createSelect();
    $select->setQuery('foo');
    $results = $client->select($select);


If you configured also another endpoint

    $client = $this->get('solarium.client.newEndpointName');
--
![PHP and Solr](img/thats-all-folks.png)
--
###Sitography
- [http://en.wikipedia.org/wiki/Apache_Solr](http://en.wikipedia.org/wiki/Apache_Solr)
- [http://lucene.apache.org/solr/](http://lucene.apache.org/solr/)
- [http://wiki.apache.org/solr/DataImportHandler](http://wiki.apache.org/solr/DataImportHandler)
- [http://wiki.apache.org/solr/CommonQueryParameters](http://wiki.apache.org/solr/CommonQueryParameters)