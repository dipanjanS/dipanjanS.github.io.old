---
layout: post
title: "Exploring Solr"
published: true
---


<div style="text-align: center;">
<img src="http://i.imgur.com/CakucSc.png"/>
</div>

<br>
I have been quite busy last couple of months with work. Today I will be talking about one of the frameworks we use to leverage search, indexing and information retrieval capabilities for analytics.

[Solr](http://lucene.apache.org/solr/) (pronounced "solar") is an open source enterprise search platform, written in Java, from the Apache Lucene project. In 2004, Solr was created by Yonik Seeley at CNET Networks as an in-house project to add search capability for the company website. Once CNET decided to open-source this framework, it was donated to the Apache Software Foundation and since then, Solr has grown steadily into a standalone top-level project (TLP), thereby attracting a robust community of users, contributors, and committers. 

Solr is written in Java and runs as a standalone full-text search server. Solr uses the Lucene Java search library at its core for full-text indexing and search, and has REST-like HTTP/XML and JSON APIs that make it usable from most popular programming languages. Solr's powerful external configuration allows it to be tailored to many types of application without Java coding, and it has a plugin architecture to support more advanced customization. Some of its major features include full-text search, hit highlighting, faceted search, real-time indexing, dynamic clustering, database integration, NoSQL features and rich document (e.g., Word, PDF) handling. Providing distributed search and index replication makes Solr is highly scalable and fault tolerant. Solr is the most popular enterprise search engine today.

Below gives a pictorial representation of some of Solr's main features.

<div style="text-align: center;">
<img src="http://i.imgur.com/J5XkCC0.png"/>
</div>

<br>
Some of the features which I found useful and have been using freqently are mentioned below.

Solr handles data belonging to various sources and formats. The schema design in Solr can be a fixed schema but there is also support for schemaless data models. Solr follows a 'convention over configuration' data driven schema where you can create new fields in your data models on the fly without explicitely setting up defined schemas.

<div style="text-align: center;">
<img src="http://i.imgur.com/TGrExbM.png"/>
</div>

<br>

Solr's query syntax uses a REST based interface for querying and we can integrate it easily with any language. It has extensive querying features for rich text analytics and searching purposes including query parsers like `dismax` and `edismax`. Besides this you can also boost query parameters, filter and sort as per your needs.
<div style="text-align: center;">
<img src="http://i.imgur.com/IdlzGvH.png"/>
</div>

<br>

Using Solr's faceting, you can aggregate, group, slice and dice your data on various fields and get valuable insights from your search queries.
<div style="text-align: center;">
<img src="http://i.imgur.com/grUmYbd.png"/>
</div>

<br>

Solr's also supports getting useful statistical measures and aggregations from different numerical fields in your data model easily which can be used further in your machine learning and analytical models.
<div style="text-align: center;">
<img src="http://i.imgur.com/cWieIYP.png"/>
</div>

<br>
Thus, you can see that Solr is definitely not just a regular search engine but one with lots of features and scope for full text searches, text analytics, aggregations and statistical operations. That's all for now, I will be talking about how to setup and start using Solr in one of my future posts. Credits for the images go to [Solr's official website](http://lucene.apache.org/solr/features.html) and some of the information regarding Solr and its history goes to [Wikipedia](https://www.wikiwand.com/en/Apache_Solr).




