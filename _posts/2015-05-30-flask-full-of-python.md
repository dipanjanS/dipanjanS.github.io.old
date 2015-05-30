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




