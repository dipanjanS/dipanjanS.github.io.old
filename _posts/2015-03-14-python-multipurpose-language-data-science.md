---
published: false
---



Today, I will be talking about one of the popular programming languages [Python](https://www.python.org) and how it has evolved over the last couple of years to become a true multi-purpose programming language. We will also be looking at how Python enables us to become better data scientists using its vast arsenal of data analysis and machine learning tools, libraries and frameworks and how should we ideally go about exploring them.

<br>
## Why Python?

Before, we begin, one might be thinking, why should we choose Python over any other programming language? I would say that no language is suitable for solving all kinds of problem, however Python being a multi-purpose programming language can be used in different domains to build solutions for different scenarios. Some examples include,

 - Web and Internet Development
 - Web Crawling and Scraping
 - Network and System Programming
 - Automation and Testing Frameworks
 - Scientific and Numeric Computing
 - Data Analytics, Visualization
 - Machine Learning and AI
 - Game Programming
 - Desktop GUIs
 - Software development, Build management and Continuous Integration

For more details refer to https://www.python.org/about/apps/

Some interesting features of Python include,

 - Python is `dynamically typed`: it means that you don't declare a type (e.g. 'integer') for a variable name, and then assign something of that type (and only that type). Instead, you have variable names, and you bind them to entities whose type stays with the entity itself. `a = 5` makes the variable name `a` to refer to the integer `5`. Later, `a = "hello"` makes the variable name `a` to refer to a string containing `"hello"`.
 - Python is `both an interpreted and compiled language`. It falls under the category, byte code interpreted. The `.py` source code is first compiled to byte code as `.pyc`. This byte code can be interpreted (official `CPython`), or JIT compiled (`PyPy`). Python source code (`.py`) can be compiled to different byte code also like `IronPython` (`.Net`) or `Jython` (`JVM`).
 - Python is `strongly typed`. It means that if `a = "5"` (the string whose value is `'5'`) will remain a string, and never coerced to a number automatically if the context requires so. Every type conversion in python must be done explicitly. 
 - Python is `object oriented`, with class-based inheritance. Everything is an object (including classes, functions, modules, etc), in the sense that they can be passed around as arguments, have methods and attributes. You can also pass functions as arguments to other functions, wrap a function with another function ( decorators ) and so on.
 - Python is a `multipurpose language`: it is not specialised to a specific set of users (like R for statistics, or PHP for web programming). It is extended through modules and libraries, that hook very easily into the C programming language.
 - Python enforces correct indentation of the code by making the indentation part of the syntax itself! There are no control braces in Python. Blocks of code are identified by the level of indentation and this improves code readability.
 - Python `can be used for any programming task`, from GUI programming to web programming with everything else in between. It's quite efficient, as much of its activity is done at the C level. Python is just a layer on top of C. 
 - Python has `libraries for everything` you can think of: game programming and openGL, GUI interfaces, web frameworks, semantic web, scientific computing, machine learning, image processing and so on.

<br>
## How Python is useful for Data Science

In simplest terms, data science is a discipline which deals with the extraction of knowledge from data using a wide variety of techniques in the fields of mathematics, statistics and computer science. One can get a better idea by looking at the following venn diagram by the famous data scientist, Drew Conway.

![](http://i.imgur.com/wBwKU8R.png)

It is clear from the above diagram that you need,
 - Hacking Skills: This is basically being able to play around with the data and use it to fit our needs including data munging, cleaning, transforming, exploring and so on.
 - Math and Statistics Knowledge: You need a solid grasp of concepts related to statistics and math to get an idea of how models work, how to tune parameters, build efficient models which are not overfit or underfit
 - Substantive Expertise: This is perhaps the most important skill because domain knowledge and expertise is really important. If you don't understand your business problem or scenario and the techniques needed for that domain, using data science techniques blindly will get you nowhere.
 
Python has a wide variety of tools and frameworks for using Data Science techniques and methodologies which we will be looking at in one of the sections below. To get a brief overview, Do check out this video by Jeremy Achin, the founder of DataRobot at PyCon 2014 in Ukraine.

<iframe width="560" height="315" src="https://www.youtube.com/embed/CoxjADZHUQA" frameborder="0" allowfullscreen></iframe>

<br>
## Python tools, frameworks and libraries for Data Science

Here I will be listing several libraries that I have found to be useful and essential if you want to be a Data Scientist. This list is not exhaustive  and I will be adding more as I come across them.

 1. ### Scientific Computing
 	> - [NumPy](http://www.numpy.org/) - Creating and manipulating numeric data in scientific computing
    > - [SciPy](http://www.scipy.org/) - High Level scientific computing
    > - [SymPy](http://www.sympy.org/en/index.html) - Symbolic mathematics and algebra
    > - [GNumPy](http://www.cs.toronto.edu/~tijmen/gnumpy.html) - Using `NumPy` on the computer GPU
    > - [IPython](http://ipython.org/) - Interactive computing in python
 
 2. ### Data Analysis and Statistical Modelling
 	> - [Pandas](http://pandas.pydata.org/) - Data Analysis tools and libraries
    > - [Statsmodels](http://statsmodels.sourceforge.net/) - Useful for building statistical models
    > - [Orange](http://orange.biolab.si/) - Another data analysis and visualization library with a GUI 
    > - [PyMVPA](http://www.pymvpa.org/index.html) - Statistical learning and analysis of large datasets. Integrates well with `scikit-learn`
    > - [PyMC](http://pymc-devs.github.io/pymc/README.html#purpose) - Useful for Bayesian Statistical Modelling and Fitting algorithms, Markov Chain Monte Carlo and so on
    
 3. ### Machine Learning, Reinforcement Learning, Deep Learning and Computer Vision
 	> - [Scikit-Learn](http://scikit-learn.org/stable/) - The most popular Machine Learning Framework
    > - [OpenCV](http://docs.opencv.org/trunk/doc/py_tutorials/py_tutorials.html) - Computer vision and image processing
    > - [Theano](http://deeplearning.net/software/theano/) - Efficient mathematical and scientific computing
 	> - [PyLearn2](https://github.com/lisa-lab/pylearn2) - Machine Learning and Deep Learning libraries based on `Theano`
    > - [Shogun](http://www.shogun-toolbox.org/) - Machine Learning toolbox with focus on Support Vector Machines
    > - [PyBrain](http://pybrain.org/) - Another ML library with focus on neural networks
    > - [Nolearn](https://github.com/dnouri/nolearn) - Wrappers around neural network libraries
    > - [Caffe](http://caffe.berkeleyvision.org/) - Deep Learning framework developed by Berkeley Vision and Learning Center
    > - [OverFeat](https://github.com/sermanet/OverFeat) - Image processing, classification and feature extraction
    > - [Hebel](https://github.com/hannes-brt/hebel) - GPU Accelerated Deep Learning library
    > - [NeuroLab](https://pypi.python.org/pypi/neurolab) - Another neural network library
 
 4. ### Data Visualization
 	> - [Matplotlib](http://matplotlib.org/) - Plotting and data visualization
 	> - [GGplot python port](https://github.com/yhat/ggplot) - Python port of popular R visualization library `ggplot2`
 	> - [Seaborn](http://stanford.edu/~mwaskom/software/seaborn/) - Python visualization library from Stanford
 	> - [Vincent](https://github.com/wrobstory/vincent) - Python wrapper for building visualizations easily based on `d3.js`
 
 5. ### Text Analytics, Information Retrieval and Natural Language Processing
 	> - [Gensim](https://radimrehurek.com/gensim/) - Text Analytics, Data Semantics and Topical Modelling
 	> - [Nltk](http://www.nltk.org/) - Text Analytics and Natural Language Processing
    > - [Requests](http://docs.python-requests.org/en/latest/) - `GET`, `POST` and much more from the web, APIs and so on
    > - [Beautiful Soup](http://www.crummy.com/software/BeautifulSoup/bs4/doc/) - Parse and extract data out of HTML and XML files
    > - [Flask](http://flask.pocoo.org/) - Lightwight python microframework for building web apps, APIs and so on
    > - [Tornado](http://www.tornadoweb.org/en/stable/) - Facebook developed this real time web framework with a scalable non-blocking server

 6. ### Big Data tools and APIs
 	> - [PySpark](https://spark.apache.org/docs/0.9.0/python-programming-guide.html) - Python APIs for [Spark](https://spark.apache.org/), used for fast in-memory data processing at scale
 	> - [PyDoop](http://crs4.github.io/pydoop/tutorial/index.html) - Python APIs for Hadoop, MapReduce and HDFS
 	> - [SolrPy](https://pypi.python.org/pypi/solrpy/) - Python API for [Solr](http://lucene.apache.org/solr/), useful for text and document retrieval, search and indexing
 
 7. ### Integration with other popular languages
 	> - R - [RPython](http://rpython.r-forge.r-project.org/) is the R wrapper
    > - Matlab - [Matpy](http://algoholic.eu/matpy/) is the Matlab wrapper
    > - Java - [Jython](http://www.jython.org/jythonbook/en/1.0/JythonAndJavaIntegration.html) is the Java wrapper
    > - Lua - [Lunatic Python](http://labix.org/lunatic-python) is the Lua wrapper
    > - Julia - [PyCall.jl](https://github.com/stevengj/PyCall.jl) is the Julia wrapper
    
<br>
## Getting up and running with Python
 
 
 
 
 
 
 





