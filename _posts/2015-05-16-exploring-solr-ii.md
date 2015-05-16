---
layout: post
title: "Exploring Solr - Part II"
published: true
---

<div style="text-align: center;">
<img src="http://i.imgur.com/ROikH1d.png"/>
</div>
<br>

In our last [article about Solr](http://dipanjans.github.io/exploring-solr/) we saw what the main features of Solr are and how some of them can be used in different scenarios and use-cases. Today we will be diving into the depths of Solr and I will be telling you, how you can setup and configure your own search server by leveraging Solr's capabilities. This article will mainly focus on getting Solr up and running and indexing some documents in the default Solr core. We will be focusing more on customization and setting up our own core and indexing custom data in a subsequent article because it is essential that you get a feel of how things work in Solr before setting up your own data cores and indexes.

## Installing Solr

Before you can start installing or using Solr, you need to make sure that you have `Java` installed in your system. You can check that easily by going to the command prompt\terminal and typing the command `java -version` or simply `java`. If you get a proper output it means that you have `java` installed. If it says that the command was not recognized, you can go to [this website](http://www.oracle.com/technetwork/java/javase/downloads/index.html) and download Java. The `JRE` would be enough, you don't need the `JDK` unless you want to write your APIs or code in Java later on, since client interaction with Solr happens over HTTP, so you can use any language that provides an HTTP client library. In addition, a number of open source client libraries are available for Solr for popular languages like .NET, Python, Ruby, PHP, and Java.

Now, you are ready to start installing Solr. For that you can do to the official Solr website and click on the download menu to go to [this page](http://lucene.apache.org/solr/mirrors-solr-latest-redir.html) which would take you to the latest version of Solr. You can also go to the archives and download any version of Solr you like from [this page](http://archive.apache.org/dist/lucene/solr/). I will be using Solr version `4.9.0` since that has been around for quite some time and it is very easy to get help online if you are stuck with troubleshooting or debugging some nasty bugs.

After downloading the archive, extract it to the directory of your choice. No installer is needed because Solr is already packed in the archive, ready to run as soon as you extract the contents. On extracting, the directory structure will be somewhat as follows. ( Do note it will be a little different in the latest version of Solr but the components will still be the same just in different folders mostly ).

<div style="text-align: center;">
<img src="http://i.imgur.com/NtbjVar.png"/>
</div>
<br>

## Starting a Solr Server

To start Solr, you just need to go to the `example` directory as shown above and type the command, `java -jar start.jar` in the terminal. On doing that, if you see something similar to this towards the end of the output which displays on the terminal, you are good to go. Do note that to stop Solr, you can simply kill the process using the PID or press `Ctrl+C` if it is running in the foreground in the terminal.

<div style="text-align: center;">
<img src="http://i.imgur.com/DwmXWid.png"/>
</div>
<br>

No with all the verbose output on the terminal, you might be wondering what exactly happened. To be clear, you now have a running version of Solr 4.9 on your computer. You can verify that Solr started correctly by directing your web browser to the Solr administration page at http://localhost:8983/solr. The below figure depicts how the administration page looks like.

<div style="text-align: center;">
<img src="http://i.imgur.com/FO7O2IU.png"/>
</div>
<br>

Behind the scenes, `start.jar` launched a Java web server named Jetty, listening on port 8983. Solr is a web application running in Jetty. The figure below, illustrates what is now running on your computer. In simple words, the Solr web application `solr.war` runs in Jetty on top of Java. There is one Solr home directory set per Jetty server, using Java system property `solr.solr.home`. Solr can host multiple cores per server, and each core has a separate directory (for example, `collection1`) containing a core-specific configuration and index (`data`) under Solr home. We will be working with the default core here like I mentioned in the beginning but in the next article we will see how we can setup our own core and index our own data and access the same in search operations.

<div style="text-align: center;">
<img src="http://i.imgur.com/P3JCF1M.png"/>
</div>
<br>

> **Note:** Not much can go wrong when starting the example server. The most common issue if the server doesn’t start correctly is that the default port `8983` is already in use by another process. If this is the case, you’ll see an error that looks like `java.net.BindException: Address already in use.` This is easy to resolve by changing the port Solr binds to. Change your start command to specify a different port for Jetty to bind to using `java -Djetty.port=8080 -jar start.jar`. Using this command, Jetty will bind to port `8080` instead of `8983`.

## Understanding Solr cores

In Solr, a core is composed of a set of configuration files, Lucene index files, and Solr’s transaction log. One Solr server running in Jetty can host multiple cores. The Solr example server you started earlier, has a single core named `collection1` which you might have figured out by now. Just as a brief aside, Solr also uses the term collection, which only has meaning in the context of a Solr cluster in which a single index is distributed across multiple servers.

Solr home is a directory structure that encapsulates one or more cores, which historically were configured by a configuration file named `solr.xml`. But as of Solr 4.4, cores can be autodiscovered and do not need to be defined in `solr.xml`. Right now, what’s important is to understand that each Solr server has one and only one Solr home directory that contains all cores. The global Java system property `solr.solr.home` sets the location of the Solr home directory. The following figure shows a directory listing of the default Solr home, `solr`, for the example server. It contains a single core named `collection1` specified in the `core.properties` file. The `collection1` directory corresponds to the core named `collection1` and contains core-specific configuration files, the Lucene index, and a transaction log.

<div style="text-align: center;">
<img src="http://i.imgur.com/zmtWAvF.png"/>
</div>
<br>

Also take note that the main Solr configuration file for a core, is named `solrconfig.xml` and `schema.xml` is the main configuration file that governs index structure and text analysis for documents and queries. We will be looking into these files in more details in the next article when we setup our own core.

## Indexing sample documents

When you first start Solr, there are no documents in the index. It’s an empty server waiting to be filled with data to search. Right now, we will index some sample data into the Solr index so that we can see how it works and query the same. We will be indexing our own data in the next article. Open a new command-line interface and enter the following:

```
cd ../example/exampledocs
java -jar post.jar *.xml
```
You should see output that looks as follows.

```
SimplePostTool version 1.5
Posting files to base url http://localhost:8983/solr/update using content-
     type application/xml..
POSTing file gb18030-example.xml
POSTing file hd.xml
POSTing file ipod_other.xml
POSTing file ipod_video.xml
POSTing file manufacturers.xml
POSTing file mem.xml
POSTing file money.xml
POSTing file monitor.xml
POSTing file monitor2.xml
POSTing file mp500.xml
POSTing file sd500.xml
POSTing file solr.xml
POSTing file utf8-example.xml
POSTing file vidcard.xml
14 files indexed.
COMMITting Solr index changes to http://localhost:8983/solr/update..
```

The `post.jar` file sends XML documents to Solr using `HTTP POST`. After all the documents are sent to Solr, the `post.jar` application issues a commit, which makes the example documents findable in Solr. To verify that the example documents were added successfully, go to the Query page in the Solr administration console (http://localhost:8983/solr) and execute the find all documents query (`*:*`). You need to select `collection1` in the dropdown box on the left to access the Query page. The following figure shows what you should see after executing the find all documents query.

<div style="text-align: center;">
<img src="http://i.imgur.com/VCaGy2V.png"/>
</div>
<br>

Thus you can see that you have a complete Solr server setup with some sample documents which you can query for just like you would do in a search engine like Google. I would recommend you to explore this interface and look at Solr's search interface and try out some queries. We will be covering how to setup a new core, index custom data into it and run queries on the same in the next article. That's all for now folks! Credits for the images and a large part of this content goes to this wonderful book [Solr in Action](http://www.manning.com/grainger/?a_aid=1&a_bid=39472865). I would recommend you check this book out and even buy it if you are serious about working with Solr.




