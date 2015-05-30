---
published: false
---


Recently, I had been working on building some custom dashboards and visualizations for one of my projects using [D3.js](http://d3js.org/) and [HTML5](https://www.wikiwand.com/en/HTML5). Since I am not primarily a frontend guy, making a RESTful API with javascript frameworks like [Node.js](https://nodejs.org/) and [Express.js](http://expressjs.com/) was definitely not my cup of tea because it would have to involve me spending hours understanding how the frameworks actually work and how they handle routing, resource, endpoint consumptions and so on. Although it seems pretty straightforward to do it given that I checked out some tutorials later on. Maybe I would try it out when I have some free time! Moving on, since we were dealing with a lot of text analytics and text data, most of our analytics algorithms were written in Python so to serve the results from an API, I thought of using the [Flask](http://flask.pocoo.org/) framework for developing the REST API and it worked out great! We are also using it for developing some of our other webservices with a lot of modules so today, I will be talking about how you can get a Flask RESTful Web Service up and running with proper API endpoints for consumption. I will also be talking about some neat tricks you can use when your Web Service starts getting big with a lot of modules and it becomes difficult to handle all the routes in one place. So lets get started!

## Installing Flask

Flask is really simple to install. Just head over to the command line and enter the following command. Do make sure `python` and `pip` are already installed.

```sh
[root@dip]#  pip install flask
```

## Hello World! with Flask

Let us now start with the typical hello world example of Flask. This is the code segment for achieving the same. The file name is `helloworld.py`

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"

if __name__ == "__main__":
    app.run(port=8888)
```

To run the app enter the following command from the terminal and you would see the app starting up as follows.

```sh
[root@dip]# python helloworld.py
 * Running on http://127.0.0.1:8888/
``` 
Now, if you go to http://localhost:8888/ you will be able to see the `Hello World!` greeting on the browser page. That's it! you have a working web app running in less than five minutes! However now we will be venturing beyond hello world and into making some RESTful Web Services.

## Monolithic Flask API

The first API we will develop will be a simple monolithic API, i.e., everything related to the API will be in a single file. Kind of like how APIs are developed using the [Bottle](http://bottlepy.org/docs/dev/index.html) framework. Following is the code snippet

```python
from flask import Flask
import json

app = Flask(__name__)

@app.route('/')
def index():
    return "Hello you are at the root level"


@app.route('/animal/dog/gooddog/<name>', methods=['GET'])
def get_gooddog_details(name):

    dog = Dog()
    dog.setName(name)
    dog.setChasesCats(False)
    return json.dumps(dog.__dict__)

@app.route('/animal/dog/baddog/<name>', methods=['GET'])
def get_baddog_details(name):

    dog = Dog()
    dog.setName(name)
    dog.setChasesCats(True)
    return json.dumps(dog.__dict__)

@app.route('/animal/cat/goodcat/<name>', methods=['GET'])
def get_goodcat_details(name):

    cat = Cat()
    cat.setName(name)
    cat.setHatesDogs(False)
    return json.dumps(cat.__dict__)

@app.route('/animal/cat/badcat/<name>', methods=['GET'])
def get_badcat_details(name):

    cat = Cat()
    cat.setName(name)
    cat.setHatesDogs(True)
    return json.dumps(cat.__dict__)

@app.route('/animal/monkey/goodmonkey/<name>', methods=['GET'])
def get_goodmonkey_details(name):

    monkey = Monkey()
    monkey.setName(name)
    monkey.setEatsBananas(True)
    return json.dumps(monkey.__dict__)

@app.route('/animal/monkey/badmonkey/<name>', methods=['GET'])
def get_badmonkey_details(name):

    monkey = Monkey()
    monkey.setName(name)
    monkey.setEatsBananas(False)
    return json.dumps(monkey.__dict__)


class Animal:

    def __init__(self):
        self.name = None
        self.species = None

    def setName(self, name):
        self.name = name

    def getName(self):
        return self.name

    def setSpecies(self, species):
        self.species = species

    def getSpecies(self):
        return self.species

    def __str__(self):
        return "%s is a %s" % (self.name, self.species)


class Dog(Animal):

    def __init__(self):
        Animal.__init__(self)
        self.chases_cats = None

    def setName(self, name):
        Animal.setName(self, name)
        Animal.setSpecies(self, "Dog")

    def setChasesCats(self, chases_cats):
        self.chases_cats = chases_cats

    def getChasesCats(self):
        return self.chases_cats


class Cat(Animal):

    def __init__(self):
        Animal.__init__(self)
        self.hates_dogs = None

    def setName(self, name):
        Animal.setName(self, name)
        Animal.setSpecies(self, "Cat")

    def setHatesDogs(self, hates_dogs):
        self.hates_dogs = hates_dogs

    def getHatesDogs(self):
        return self.hates_dogs


class Monkey(Animal):

    def __init__(self):
        Animal.__init__(self)
        self.eats_bananas = None

    def setName(self, name):
        Animal.setName(self, name)
        Animal.setSpecies(self, "Monkey")

    def setEatsBananas(self, eats_bananas):
        self.eats_bananas = eats_bananas

    def getEatsBananas(self):
        return self.eats_bananas


app.run(debug=True, port=8888)
```

So basically, everything related to the app is in one single monolithic file. We have created API endpoints related to a few animals here using Inheritance. To get a detailed idea about inheritance check out this [awesome tutorial here](http://www.jesshamrick.com/2011/05/18/an-introduction-to-classes-and-inheritance-in-python/). Once you start running the code, here are a few responses you get for some of the API calls.

`GET` request for `http://localhost:8888/animal/dog/gooddog/bruzo`

```json
{
    "chases_cats": false,
    "name": "bruzo",
    "species": "Dog"
}
```

`GET` request for `http://localhost:8888/animal/cat/badcat/whiskers`

```json
{
    "hates_dogs": true,
    "name": "whiskers",
    "species": "Cat"
}
```

Thus you have a working API up and running with several endpoints. Now, I will show you how you can organize the same API using proper separation of concerns which will help you out once your project starts to get bigger and bigger.

## Flask API with Separation of Concerns

We now create the same app but now re-factoring the code and restructuring the code files using proper separation of concerns. The folder structure used is depicted below.

```
    /soc-app
      |
      +---- /socapp
      |       |
      |       +---- __init__.py
      |       +---- model.py
      |       +---- routes.py
      +---- run.py
      
  ```
  
The code for the different files are depicted below.
  
Code for `run.py`
  
```python
from flask import Flask

app = Flask(__name__)

import socapp.routes
```

Code for `/socapp/__init__.py`
  
```python
from flask import Flask

app = Flask(__name__)

import socapp.routes
```

Code for `/socapp/model.py`
  
```python
class Animal:

    def __init__(self):
        self.name = None
        self.species = None

    def setName(self, name):
        self.name = name

    def getName(self):
        return self.name

    def setSpecies(self, species):
        self.species = species

    def getSpecies(self):
        return self.species

    def __str__(self):
        return "%s is a %s" % (self.name, self.species)


class Dog(Animal):

    def __init__(self):
        Animal.__init__(self)
        self.chases_cats = None

    def setName(self, name):
        Animal.setName(self, name)
        Animal.setSpecies(self, "Dog")

    def setChasesCats(self, chases_cats):
        self.chases_cats = chases_cats

    def getChasesCats(self):
        return self.chases_cats


class Cat(Animal):

    def __init__(self):
        Animal.__init__(self)
        self.hates_dogs = None

    def setName(self, name):
        Animal.setName(self, name)
        Animal.setSpecies(self, "Cat")

    def setHatesDogs(self, hates_dogs):
        self.hates_dogs = hates_dogs

    def getHatesDogs(self):
        return self.hates_dogs


class Monkey(Animal):

    def __init__(self):
        Animal.__init__(self)
        self.eats_bananas = None

    def setName(self, name):
        Animal.setName(self, name)
        Animal.setSpecies(self, "Monkey")

    def setEatsBananas(self, eats_bananas):
        self.eats_bananas = eats_bananas

    def getEatsBananas(self):
        return self.eats_bananas

```

Code for `/socapp/routes.py`
  
```python
from socapp import app
import json
from model import Dog, Cat, Monkey

@app.route('/')
def index():
    return "Hello you are at the root level"


@app.route('/animal/dog/gooddog/<name>', methods=['GET'])
def get_gooddog_details(name):

    dog = Dog()
    dog.setName(name)
    dog.setChasesCats(False)
    return json.dumps(dog.__dict__)

@app.route('/animal/dog/baddog/<name>', methods=['GET'])
def get_baddog_details(name):

    dog = Dog()
    dog.setName(name)
    dog.setChasesCats(True)
    return json.dumps(dog.__dict__)

@app.route('/animal/cat/goodcat/<name>', methods=['GET'])
def get_goodcat_details(name):

    cat = Cat()
    cat.setName(name)
    cat.setHatesDogs(False)
    return json.dumps(cat.__dict__)

@app.route('/animal/cat/badcat/<name>', methods=['GET'])
def get_badcat_details(name):

    cat = Cat()
    cat.setName(name)
    cat.setHatesDogs(True)
    return json.dumps(cat.__dict__)

@app.route('/animal/monkey/goodmonkey/<name>', methods=['GET'])
def get_goodmonkey_details(name):

    monkey = Monkey()
    monkey.setName(name)
    monkey.setEatsBananas(True)
    return json.dumps(monkey.__dict__)

@app.route('/animal/monkey/badmonkey/<name>', methods=['GET'])
def get_badmonkey_details(name):

    monkey = Monkey()
    monkey.setName(name)
    monkey.setEatsBananas(False)
    return json.dumps(monkey.__dict__)

```
From the above structure, you can see this is a pretty neat way to organize your code. You have your main app where `__init__.py` instantiates your flask app and you have all data model related classes and properties in the `model.py` file and the API routes are defined in `routes.py`. To run the app just run the command `python run.py` from the terminal and you can access the API using the same access patterns as we had shown earlier.


## Flask API with further modularizations

Here we will modularize our existing codebase further making necessary changes and introducing further separation of concerns with regards to our models and modules which do the routing and act as controllers if you consider a typical MVC pattern. The folder structure for the new app will be as follows

```
    /modularized-app
      |
      +---- /socapp
      |       |
      |       +---- __init__.py
      |       +---- /models
      |       |       |
      |       |       +---- /animal
      |       |       |       |
      |       |       |       +---- __init__.py
      |       |       |       +---- model.py
      |       |       +---- /cat
      |       |       |       |
      |       |       |       +---- __init__.py
      |       |       |       +---- model.py
      |       |       +---- /dog
      |       |       |       |
      |       |       |       +---- __init__.py
      |       |       |       +---- model.py
      |       |       +---- /monkey
      |       |               |
      |       |               +---- __init__.py
      |       |               +---- model.py
      |       +---- /modules
      |               |
      |               +---- __init__.py
      |               +---- /cat
      |               |       |
      |               |       +---- __init__.py
      |               |       +---- routes.py
      |               +---- /dog
      |               |       |
      |               |       +---- __init__.py
      |               |       +---- routes.py
      |               +---- /monkey
      |                       |
      |                       +---- __init__.py
      |                       +---- rputes.py
      +---- run.py
      
  ```
  
Thus code in the `run.py` file essentially remains the same because it is just concerned with running the app on the server. The code in the `models` package changes essentially where each sub-package in the `models` package now has its own model which is basically denoted by `model.py` and it just deals with its own model and related methods. Do note that this app is extremely simplistic hence you may get an idea that such a level of granular separation and modularization may not be needed but when your apps start getting huge, you need to have separate models and related methods for that. Example if you consider a Blog model, you need models like `posts`, `users`, `comments`, `login` and so on which will help you keep the right level of separation. Do note that the previous model works fine for small scale apps so it all boils down to the level of complexity involving your app and its access patterns. The `modules` package essentially deals with the different modules and their routes based on the API access endpoints which is handled by `routes.py` for each module like `dog`, `cat` and so on.

Some of the sample code is shown below for each type of package in the above app.

Code for `/socapp/__init__.py`

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def index():
    return "Hello you are at the root level"

import socapp.modules.cat.routes
import socapp.modules.dog.routes
import socapp.modules.monkey.routes
```

Code for `/socapp/models/cat/model.py`

```python
from socapp.models.animal.model import Animal


class Cat(Animal):

    def __init__(self):
        Animal.__init__(self)
        self.hates_dogs = None

    def setName(self, name):
        Animal.setName(self, name)
        Animal.setSpecies(self, "Cat")

    def setHatesDogs(self, hates_dogs):
        self.hates_dogs = hates_dogs

    def getHatesDogs(self):
        return self.hates_dogs
```       

Code for `/socapp/modules/cat/routes.py`

```python
from socapp import app
import json
from socapp.models.cat.model import Cat


@app.route('/animal/cat/goodcat/<name>', methods=['GET'])
def get_goodcat_details(name):

    cat = Cat()
    cat.setName(name)
    cat.setHatesDogs(False)
    return json.dumps(cat.__dict__)

@app.route('/animal/cat/badcat/<name>', methods=['GET'])
def get_badcat_details(name):

    cat = Cat()
    cat.setName(name)
    cat.setHatesDogs(True)
    return json.dumps(cat.__dict__)
```
To run this app you need to run the same command as you did for the previous app. Thus you can see that it is not that hard to refactor the codebase and introduce the right level of modularization which helps us think long term changes and be prepared for introducing new features and functionality to our API without spending much time on editing and deleting large chunks of code later on.


## Introducing blueprints to our API

You may have noticed in the previous APIs that essentially even though we were modularizing the code, the route names were unnecessarily long and a part of the route was being repeated in each endpoint. Blueprints provide us an opportunity to overcome this and also utlize modules efficiently to their full potential. To quote from the Flask page on [blueprints](http://flask.pocoo.org/docs/0.10/blueprints/),

> Flask uses a concept of blueprints for making application components and supporting common patterns within an application or across applications. Blueprints can greatly simplify how large applications work and provide a central means for Flask extensions to register operations on applications. A Blueprint object works similarly to a Flask application object, but it is not actually an application. Rather it is a blueprint of how to construct or extend an application.

Blueprints in Flask are intended for these cases:

 - Factor an application into a set of blueprints. This is ideal for larger applications; a project could instantiate an application object, initialize several extensions, and register a collection of blueprints.
 - Register a blueprint on an application at a URL prefix and/or subdomain. Parameters in the URL prefix/subdomain become common view arguments (with defaults) across all view functions in the blueprint.
 - Register a blueprint multiple times on an application with different URL rules.
 - Provide template filters, static files, templates, and other utilities through blueprints. A blueprint does not have to implement applications or view functions.
 - Register a blueprint on an application for any of these cases when initializing a Flask extension.

