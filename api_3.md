# Introduction

For the issue type "Information disclosure" i wanted to give you guys an example since it does no seem to be very well understood what data counts as sensitive and what should be disclosed as public information.

# Requirements

- A patato ... Seriously though, a small VPS or spare computer with the minimal amount of RAM and disk space will do. The APIs we will be building do not require much.
- Python 3.x ([https://www.python.org/downloads/](https://www.python.org/downloads/))
- Flask (pip install Flask but hold off if you did not do it yet, we will be creating a python virtual env)

# Let's set up

To start with, we will need to set up a virtual environment first. This is a place we can install our dependencies of a certain project on and keep them seperate from the other projects. This is very useful to keep oversight but also if you have one project that requires a certain version of an import while another project might need a much older and non-compatible version of that library. 

```bash
mkdir "GoudAPI-infodisclosure"
cd GoudAPI-infodisclosure
python3 -m venv GoudAPI-infodisclosure
```

```bash
mkdir GoudAPI-infodisclosure
cd GoudAPI-infodisclosure
py -3 -m venv GoudAPI-infodisclosure
```

With these commands we are creating a venv (virtual enviornment) called GoudAPI-BAC which is marked by a new folder, now we have to swith to it. 

```bash
. GoudAPI-infodisclosure/bin/activate
```

```bash
GoudAPI-infodisclosure\Scripts\activate
```

And now we can easily use pip to install flask

```bash
pip install Flask
```

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d7c71df8-fcd7-444c-9b3d-f924b0eacd8e/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d7c71df8-fcd7-444c-9b3d-f924b0eacd8e/Untitled.png)

Now that flask is installed, we can easily create our first vulnerable API.

# Hello World

We will first need to write a few lines of code to tell python it should start a flask web application.

```bash
import flask
from flask import request, jsonify

app = flask.Flask(__name__)
app.config["DEBUG"] = True
```

These lines will tell python to import flask and to set it up with debugging enabled. app.config["DEBUG"] = True refers to a variable from the flask config. We could set up a flask config file seperatly but let's keep it all in one place for now.

We also want to import request and jsonify for later since our first API will only return static data so let's build up that static data with the following code.

```bash
# Create some test data for our catalog in the form of a list of dictionaries.
CreditCards = [
{'id': 0,
     'creditCard': '1234567901234',
     'user': 'API',
     'CVV': '677',
     'validUntil': '1992'},
{'id': 1,
     'creditCard': '1234567901235',
     'user': 'API',
     'CVV': '677',
     'validUntil': '2022'},
{'id': 2,
     'creditCard': '1234567901239',
     'user': 'API',
     'CVV': '677',
     'validUntil': '2023'}
]

```

So now that we have some data, we need to define a way to get that data. In the next step we will define the path /api/v1/resources/creditCards/all and the possible methods. When we make a GET request on /api/v1/resources/creditCards/all we will return the data from the previous step.

```bash
# A route to return all of the available entries in our catalog.
@app.route('/api/v1/resources/creditCards/all', methods=['GET'])
def api_all():
    return jsonify(CreditCards)
```

all that is left is for us to start up our application but as of now flask does not know how to start yet so let's tell it. We will also be using another port in this case because port 5000 (the default port) will already be taken from our previous script. 

```bash
app.run(host="0.0.0.0",port="5002")
```

Combine all of this into one script and it will start up when we ask it, one last thing i want to highlight here is the host="0.0.0.0" argument. We do not need this but if we do not give it this argument, the API will only be accesible from localhost. The default port for flask will be 5000 but we told our script to run on port 5002 so make sure nothing else is running on that port.

Combine all of that into a file and we are ready to go.

```bash
python training1.py
```

replace [training1.py](http://training1.py) with whatever you named your file.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/35f7fbe6-2e9d-47a3-99c1-2fdced06453a/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/35f7fbe6-2e9d-47a3-99c1-2fdced06453a/Untitled.png)

And with that, we should be able to surf to our VPS' IP adres on port 5002 and see what our flask app is saying. 

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4ebfdaf0-4a7c-4295-9269-0d5b4369ff42/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4ebfdaf0-4a7c-4295-9269-0d5b4369ff42/Untitled.png)

To get some data, we need to surf to the path we defined earlier. "/api/v1/resources/cheese/all"

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/40bab8e7-ec3d-4fe5-b41a-a4dbbf9e8325/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/40bab8e7-ec3d-4fe5-b41a-a4dbbf9e8325/Untitled.png)

Make sure this works as this will be our basefile for the rest of the exercises. 

We can also surf to "/api/v1/creditCards/cheese/all" to find we are stringifying our whole credit cards array with all of its data, including CVV and expiration data. If a MiTM attack would occur this would lead to a much more severe attack than should happen.

# Extra: Full code

```python
import flask
from flask import request, jsonify

app = flask.Flask(__name__)
app.config["DEBUG"] = True

CreditCards = [
{'id': 0,
     'creditCard': '1234567901234',
     'user': 'API',
     'CVV': '677',
     'validUntil': '1992'},
{'id': 1,
     'creditCard': '1234567901235',
     'user': 'API',
     'CVV': '677',
     'validUntil': '2022'},
{'id': 2,
     'creditCard': '1234567901239',
     'user': 'API',
     'CVV': '677',
     'validUntil': '2023'}
]

# A route to return all of the available entries in our catalog.
@app.route('/api/v1/resources/creditCards/all', methods=['GET'])
def api_all():
    return jsonify(CreditCards)

app.run(host="0.0.0.0",port="5002")
```

# Extra: Another example full code

This script returns the commenters full address and while it might not be displayed in the UI persÃ©, it is vulnerable if the API returns the information like this.

```jsx
import flask
from flask import request, jsonify

app = flask.Flask(__name__)
app.config["DEBUG"] = True

# Create some test data for our catalog in the form of a list of dictionaries.
comments = [
    {'id': 0,
     'comment': '1234567901234',
     'user': 'testUser',
     'user email': 'test@bla.com',
     'user adress': 'testlane, testing - 340043 testing in testland'}
]

@app.route('/', methods=['GET'])
def home():
    return '''<h1>Distant Reading Archive</h1>
<p>A prototype API for distant reading of science fiction novels.</p>'''

# A route to return all of the available entries in our admin log.
@app.route('/api/v1/comments', methods=['GET'])
def api_cards():
    if 'id' in request.args:
        id = int(request.args['id'])
    else:
        return "Error: No id field provided. Please specify an id."

    # Create an empty list for our results
    results = []

    # Loop through the data and match results that fit the requested ID.
    # IDs are unique, but other fields might return many results
    for comment in comments:
        if comment['id'] == id:
            results.append(comment)

    # Use the jsonify function from Flask to convert our list of
    # Python dictionaries to the JSON format.
    return jsonify(results)

app.run(host="0.0.0.0",port="5005")
```
