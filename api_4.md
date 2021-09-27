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
from flask import request

app = flask.Flask(__name__)
app.config["DEBUG"] = True
```

These lines will tell python to import flask and to set it up with debugging enabled. app.config["DEBUG"] = True refers to a variable from the flask config. We could set up a flask config file seperatly but let's keep it all in one place for now.

We also want to import request for later since our first API will only return static data so let's build up that static data with the following code.

```bash
@app.route('/', methods=['POST'])
def api_home():
     return "paths '/api/v1/changeUserSettings'"
```

By adding this basepath, we will allow the user to see the endpoints, there will only be only so we display it and we can move on the next path.

```bash
# A route to return all of the available entries in our admin log.
@app.route('/api/v1/changeUserSettings', methods=['POST'])
def api_cards():
    if 'username' in request.args:
        name = request.args['name']
    else:
        return "Error: No username field provided. Please specify an accountType,name,firstname and adress."

    if 'name' in request.args:
        name = request.args['name']
    else:
        return "Error: No name field provided. Please specify an accountType,name,firstname and adress."

    if 'firstname' in request.args:
        name = request.args['name']
    else:
        return "Error: No firstname field provided. Please specify an accountType,name,firstname and adress."

    if 'adress' in request.args:
        name = request.args['name']
    else:
        return "Error: No adress field provided. Please specify an accountType,name,firstname and adress."

    if 'accountType' in request.args:
        type = request.args['accountType']
    else:
        return "Error: No type field provided. Please specify an accountType,name,firstname and adress. The type can be either user or reader"

    if type == 'admin':
        return "Good job!! Flag: (21384324-03240324)"
    else:
        return "account settings saved"
```

This is where the cheating really begins because as you can the user never really gets updated. The server also returns some arguments and actually shows you exactly what parameters are required which you would need a swagger for IRL but this is just the beginning of our easy APIs. 

```bash
app.run(host="0.0.0.0",port="5006")
```

Combine all of this into one script and it will start up when we ask it, one last thing i want to highlight here is the host="0.0.0.0" argument. We do not need this but if we do not give it this argument, the API will only be accesible from localhost. The default port for flask will be 5000 but we told our script to run on port 5006 so make sure nothing else is running on that port.

Combine all of that into a file and we are ready to go.

```bash
python training1.py
```

replace [training1.py](http://training1.py) with whatever you named your file.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/35f7fbe6-2e9d-47a3-99c1-2fdced06453a/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/35f7fbe6-2e9d-47a3-99c1-2fdced06453a/Untitled.png)

And with that, we should be able to surf to our VPS' IP adres on port 5006 and see what our flask app is saying. 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c653f1af-ad1a-4eee-be4f-b4936c578da7/Untitled.png)

To get some data, we need to surf to the path in a POST method. This we will do with a tool called postman, you should know it by now because there is a chapter in the course about it.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/326ad6b3-d8dd-4b86-bbf7-627f418198a1/Untitled.png)

As you can see the server is returning the path we programmed in earlier. To get this result, simply fill in the URL and make sure you set the method to POST. 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ef5f6c47-a11d-47f1-8276-38262ca2f833/Untitled.png)

Surfing to this URL will give us the result that the server needs some parameters, most might be inclined to fill in all of them so let's do that. 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/674dc4b0-fea3-4551-99e0-f929d198e4a8/Untitled.png)

It might seem like we are stuck here but let's try to remove some things like the accountType.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a5021280-87c0-4cd5-8c7a-91d9034fa772/Untitled.png)

Now we get a different message that is saying that the account type can only be certain types but what if we try admin?

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/40a34d7a-7af8-416f-9c2a-91ceb00bd890/Untitled.png)

# Extra: Full code

```python
import flask
from flask import request, jsonify

app = flask.Flask(__name__)
app.config["DEBUG"] = True

@app.route('/', methods=['POST'])
def api_home():
     return "paths '/api/v1/changeUserSettings'"

# A route to return all of the available entries in our admin log.
@app.route('/api/v1/changeUserSettings', methods=['POST'])
def api_cards():
    if 'username' in request.args:
        name = request.args['name']
    else:
        return "Error: No username field provided. Please specify an accountType,name,firstname and adress."

    if 'name' in request.args:
        name = request.args['name']
    else:
        return "Error: No name field provided. Please specify an accountType,name,firstname and adress."

    if 'firstname' in request.args:
        name = request.args['name']
    else:
        return "Error: No firstname field provided. Please specify an accountType,name,firstname and adress."

    if 'adress' in request.args:
        name = request.args['name']
    else:
        return "Error: No adress field provided. Please specify an accountType,name,firstname and adress."

    if 'accountType' in request.args:
        type = request.args['accountType']
    else:
        return "Error: No type field provided. Please specify an accountType,name,firstname and adress. The type can be either user or reader"

    if type == 'admin':
        return "Good job!! Flag: (21384324-03240324)"
    else:
        return "account settings saved"

app.run(host="0.0.0.0",port="5006")
```
