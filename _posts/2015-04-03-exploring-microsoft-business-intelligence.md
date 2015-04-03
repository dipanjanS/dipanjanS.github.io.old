---
layout: post
title: "Business Intelligence: Exploring the Microsoft BI Stack"
published: true
---


<div style="text-align: center;">
<img src="http://i.imgur.com/z142rTI.png"/>
</div>

<br>
Today, I will be talking about Business Intelligence and how to leverage Microsoft's Business Intelligence Stack to get valuable insights and extract the maximum information out of your enterprise data. Recently, one of my projects required me to do some analysis of data to improve existing processes in the organization. The best way to do so, was to get the data from the underlying databases, build a proper data semantic model and generate reports from that to view insights and comprehend them easily. In pursuit of solving this use case, I explored the Microsoft BI Stack and realised its excellent potential for tacking these scenarios. 

Before we begin, I will describe briefly what Business Intelligence (BI) indicates, since we will be talking about tools which enable us to do BI better.**Business intelligence (BI)** is the set of techniques and tools for the transformation of raw data into meaningful and useful information for business analysis purposes. BI can be used to support a wide range of business decisions ranging from operational to strategic. Basic operating decisions include product positioning or pricing. Strategic business decisions include priorities, goals and directions at the broadest level. BI technologies provide historical, current and predictive views of business operations. Common functions of business intelligence technologies are reporting, online analytical processing, analytics, data mining, process mining, complex event processing, business performance management, benchmarking, text mining, predictive analytics and prescriptive analytics. For more details you can check out [this Wikipedia page on BI](https://www.wikiwand.com/en/Business_intelligence). 

The Microsoft BI Stack consists of several tools which are mostly indicated in the picture shown below.

<div style="text-align: center;">
<img src="http://i.imgur.com/9NB2SVm.png"/>
</div>

<br>
The main capabilities of the Microsoft BI Stack include,

>- **Self-service:** Excel provides new self-service capabilities and empowers users with data discovery, analysis, and visual exploration. You can now uncover hidden insights and facilitate ease of collaboration and access from anywhere through HTML5 and mobile applications.
- **Dashboards and Reporting:** SharePoint Server provides a full set of rich dashboard and scorecard capabilities including advanced filtering, guided navigation, interactive analytics, and visualizations. SQL Server Reporting Services is a comprehensive, highly scalable solution providing operational reporting for pixel-perfect printing and browser-based viewing, as well as ad-hoc data exploration and visualization.
- **Analysis:** SQL Server Analysis Services empowers you to build comprehensive, enterprise-scale analytic solutions that can also leverage in-memory technology and provide interactive exploration of aggregated data.
- **Predictive analytics:** SQL Server predictive analytics combined with the simplicity and familiarity of Excel provides a highly advanced data mining solution.

<br>
# **<u>Self-service</u>**
Microsoft empowers users with a complete self-service business intelligence (BI) solution delivered through Excel and Power BI for Office 365. Excel provides data discovery, analysis, and visualization capabilities that help users identify deeper business insights from their data. Power BI for Office 365 provides an easy-to-deploy cloud-based BI environment for users to share, collaborate, and access data and reports from anywhere.

## Capabilities
### Data discovery and transformation
Power Query in Excel enables business users to more easily search, discover, combine and transform data from multiple data sources that are both inside and outside the company.

### Modeling and analysis
Power Pivot in Excel enables business users to create powerful analytical models, and in-memory capabilities allow users to manipulate and work with large volumes of data quickly.

### Visualization
Power View and Power Map in Excel provide visual data exploration with ad-hoc interactive reporting and geospatial analysis to enable users to uncover insights and present findings.

### BI sites
Power BI for Office 365 allows business users to quickly create BI sites for teams to share reports and data sets. Larger workbook viewing is now supported (up to 250MB) which allows users to view and interact with larger reports through their browser.

### Share queries
With the data catalog in Power BI for Office 365, business users can share not only their workbooks, but also the original data queries they created in Excel, to maintain data views for colleagues to consume in their own reports.

### Natural language questions
With Q&A, users type ad-hoc questions and the system responds with answers in the form of interactive charts and graphs. The experience is immediate and uses natural language query to help interpret the semantics of questions being asked.

### Data Catalog
With Power BI for Office 365, IT departments can register corporate data with the Data Catalog search engine, making corporate data available in Power Query for Excel, and provide an easier data search experience for business users.

### Mobile access
Mobile BI access to reports in Power BI for Office 365 is provided through new HTML5 support and a native mobile application for Windows 8 tablets.

<img src="http://i.imgur.com/4CSHlHq.png"/>

<br>
# **<u>Dashboards and Reports</u>**
SharePoint Server provides an integrated environment to simplify report and dashboard management and delivery across the organization. Built-in capabilities for dashboard and scorecard creation offer advanced filtering, guided navigation, interactive analytics, and visualizations. SQL Server Reporting Service delivers highly interactive and explorative ad-hoc reports with Power View for SharePoint. Pixel-perfect reporting and full integration with SharePoint Server allows for ease of distribution and management.

## Capabilities
### The right information
- Build scorecards and aggregate content from multiple sources with the dashboard designer
- Use advanced filtering, guided navigation, interactive analytics and visualizations for ad-hoc exploration
- Easily create applications from a single development platform

### Ad-hoc data visualization with Power View for SharePoint
- Inspire users with an immersive ad-hoc reporting environment that enables faster insights
- Empower end users to create compelling visualizations that are presentation ready
- Share insights with tight SharePoint integration

### Professional, precisely formatted reports
- Enable IT professionals to create reports seamlessly with Report Builder, a fully-featured authoring tool with the familiar Microsoft Office look and feel
- Render reports to a variety of formats, including the new Office format for Word and Excel, as well as PDF, TIFF, and HTML
- Allow sharing of objects for faster, more agile report development with Report Parts

### Robust management and scalability
- Manage your reporting environment with SharePoint integration
- Execute scheduled reports, manage report subscriptions, and control access to generated reports
- Scale your environment from a few reports to a corporate-wide deployment

<img src="http://i.imgur.com/LnNL27N.png"/>

<br>
# **<u>Analysis</u>**
With the SQL Server Analysis Services platform, you can build high performance analytical models (multidimensional and tabular) that can be used for interactive data analysis, reporting, and visualization. SQL Server provides a comprehensive analytical and modeling experience to support rapid solution prototyping and support for the largest enterprise-grade solutions.

## Capabilities
### Consistent BI semantic model
- Build a single model for BI apps, reporting, analysis, dashboards and scorecards, providing users a consistent view of their data
- Implement powerful models that allow for flexible design and creation of business logic, including access to data for real-time analysis

### Robust performance and scale
- Benefit from Analysis Service’s in-memory analytics technology, multi-threading and linear scaling with multiple cores
- Optimize multi-dimensional performance and eliminate unnecessary aggregations with an improved Aggregation Designer
- Enable real-time updates with Proactive Caching

<img src="http://i.imgur.com/EoS66a0.png"/>

<br>
# **<u>Predictive analytics</u>**
Mining historical data can provide new insights, form a reliable basis for accurate forecasting and help companies make better decisions. SQL Server provides a spectrum of technology that helps customers build predictive analytics solutions that integrate into the tools that are already familiar to most users. The business analyst can manipulate advanced data mining tools in Excel with the Data Mining Add-ins, while developers can build sophisticated data-mining solutions using familiar SQL Server Development Tools and IT can manage the solution from SQL Server Management Studio. Each person plays a role in creating a solution and each player can interact with the solution from a familiar environment.

## Capabilities
### Rely on a complete predictive analytics platform
- Use rich and innovative algorithms like inventory forecast and most profitable customer identification
- Uncover non-intuitive data relationships and identify trends in unstructured data
- Deliver insights with Microsoft Office by using a familiar Excel environment with the Data Mining Add-ins

### Integrate predictive analysis everywhere
- Extract quality data by using in-flight mining for ETL data loads
- Perform insightful analysis by including data-mining results as dimensions in your Analysis Services cubes
- Add prediction functions to calculations and key performance indicators
- Natively integrate reporting by using data-mining queries as the source in Reporting Services
- Surface predictive KPIs in SharePoint Server

### Extend predictive analysis
- Build applications by using predictive programming, and take advantage of XMLA, Data Mining extensions, ADOMD.NET, OLE DB, and Analysis Management objects
- Customize algorithms and visualizations using procedures, predictive model markup language, algorithms and visualizations

<img src="http://i.imgur.com/PzPMv2u.png"/>


With tools like SSAS, you can load in data from different sources be it MS SQL databases and warehouses, Oracle, Teradata, CSVs, Flat files and much more. Once you have this data in SSAS, you can build semantic models and perform various analytics on the data as required. Once the BI Semantic Model is built and deployed, you can use tools like SSRS, PowerView and so on to build dashboards and reports on top of this model and present insights in different ways which are both appealing and informative.

Coming to the BI Semantic Models, there are two types of Data models which are supported in Microsoft. They are explained below.
 - **Multi-dimensional models:** The multidimensional model uses cube structures for analyzing business data across multiple dimensions. Multidimensional mode is the default server mode of Analysis Services. It includes a query and calculation engine for OLAP data, with MOLAP, ROLAP, and HOLAP storage modes to balance performance with scalable data requirements. 
 - **Tabular models:** Tabular models are in-memory databases in Analysis Services. Using state-of-the-art compression algorithms and multi-threaded query processor, the xVelocity in-memory analytics engine (VertiPaq) delivers fast access to tabular model objects and data by reporting client applications such as Microsoft Excel and Microsoft Power View. Tabular models support data access through two modes: Cached mode and DirectQuery mode. In cached mode, you can integrate data from multiple sources including relational databases, data feeds, and flat text files. In DirectQuery mode, you can bypass the in-memory model, allowing client applications to query data directly at the (SQL Server relational) source.

The Microsoft BI Semantic Model architecture is shown in the picture below.

<div style="text-align: center;">
<img src="http://i.imgur.com/vPIRR6I.png"/>
</div>

<br>
For presenting your insights and analysis from the data models, you can use a wide variety of tools as I had mentioned above and these include SSRS, PowerView, PerformancePoint, Excel, Power BI and so on. A sample dashboard is shown below.

<div style="text-align: center;">
<img src="http://i.imgur.com/BZ7fxVp.png"/>
</div>

<br>
Thus you can see that the Microsoft BI Stack is quite diverse and consists of a large toolkit. Sometimes the different tools can be confusing because of their sheer number and extremely long names! But don't let that discourage you because once you start using them, you will definitely love it if you are a data scientist\engineer\analysis or a BI developer. Thats all for now, hope you enjoy building business intelligence solutions with the Microsoft BI Stack. Credits for the information and images go to Microsoft.

