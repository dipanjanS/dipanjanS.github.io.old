---
published: false
---




In our last [article about Solr](http://dipanjans.github.io/exploring-solr/) we saw what the main features of Solr are and how some of them can be used in different scenarios and use-cases. Today we will be diving into the depths of Solr and I will be telling you, how you can setup and configure your own search server by leveraging Solr's capabilities. This article will mainly focus on getting Solr up and running and indexing some documents in the default Solr core. We will be focusing more on customization and setting up our own core and indexing custom data in a subsequent article because it is essential that you get a feel of how things work in Solr before setting up your own data cores and indexes.

Before you can start installing or using Solr, you need to make sure that you have `Java` installed in your system. You can check that easily by going to the command prompt\terminal and typing the command `java -version` or simply `java`. If you get a proper output it means that you have `java` installed. If it says that the command was not recognized, you can go to [this website](http://www.oracle.com/technetwork/java/javase/downloads/index.html) and download Java. The `JRE` would be enough, you don't need the `JDK` unless you want to write your APIs or code in Java later on, since client interaction with Solr happens over HTTP, so you can use any language that provides an HTTP client library. In addition, a number of open source client libraries are available for Solr for popular languages like .NET, Python, Ruby, PHP, and Java.

Now, you are ready to start installing Solr. For that you can do to the official Solr website and click on the download menu to go to [this page](http://lucene.apache.org/solr/mirrors-solr-latest-redir.html) which would take you to the latest version of Solr. You can also go to the archives and download any version of Solr you like from [this page](http://archive.apache.org/dist/lucene/solr/). I will be using Solr version `4.9.0` since that has been around for quite some time and it is very easy to get help online if you are stuck with troubleshooting or debugging some nasty bugs.

After downloading the archive, extract it to the directory of your choice. No installer is needed because Solr is already packed in the archive, ready to run as soon as you extract the contents. On extracting, the directory structure will be somewhat as follows. ( Do note it will be a little different in the latest version of Solr but the components will still be the same just in different folders mostly ).

<div style="text-align: center;">
<img src="http://i.imgur.com/NtbjVar.png"/>
</div>
<br>

To start Solr, you just need to go to the `example` directory as shown above and type the command, `java -jar start.jar` in the terminal. On doing that, if you see something similar to this towards the end of the output which displays on the terminal, you are good to go.

<div style="text-align: center;">
<img src="http://i.imgur.com/DwmXWid.png"/>
</div>
<br>

