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
              |
              +---- __init__.py
              +---- model.py
              +---- routes.py
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

