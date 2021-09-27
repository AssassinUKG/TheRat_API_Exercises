# Introduction

For the issue type "Broken authentication" There are many things that can go wrong but i wanted to show you that broken authentication 

# Requirements

- A patato ... Seriously though, a small VPS or spare computer with the minimal amount of RAM and disk space will do. The APIs we will be building do not require much.
- Python 3.x ([https://www.python.org/downloads/](https://www.python.org/downloads/))
- Flask (pip install Flask but hold off if you did not do it yet, we will be creating a python virtual env)

# Let's set up

To start with, we will need to set up a virtual environment first. This is a place we can install our dependencies of a certain project on and keep them seperate from the other projects. This is very useful to keep oversight but also if you have one project that requires a certain version of an import while another project might need a much older and non-compatible version of that library. 

```bash
mkdir "GoudAPI-SQLi"
cd GoudAPI-SQLi
python3 -m venv GoudAPI-SQLi
```

```bash
mkdir GoudAPI-SQLi
cd GoudAPI-SQLi
py -3 -m venv GoudAPI-SQLi
```

With these commands we are creating a venv (virtual enviornment) called GoudAPI-BAC which is marked by a new folder, now we have to swith to it. 

```bash
. GoudAPI-SQLi/bin/activate
```

```bash
GoudAPI-SQLi\Scripts\activate
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
from flask import Flask, render_template, redirect, url_for, request

app = flask.Flask(__name__)
app.config["DEBUG"] = True
```

These lines will tell python to import flask and to set it up with debugging enabled. app.config["DEBUG"] = True refers to a variable from the flask config. We could set up a flask config file seperatly but let's keep it all in one place for now.

We also want to import request for later since our first API will only return static data so let's build up that static data with the following code. In this system we are going to work with templates as well to show the login system. This template is a seperate file and we need to place it in a "templates folder". For example if the script is in /root then we need to add /root/templates. This is simply a "html file"

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5126f9ea-51a3-4d47-9e9b-74673cf64dfc/Untitled.png)

```html
<html>
  <head>
    <title>Flask Intro - login page</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link href="static/bootstrap.min.css" rel="stylesheet" media="screen">
  </head>
  <body>
    <div class="container">
      <h1>Please login</h1>
      <h2>The username can be found somewhere here on the page. The password? I don't know :D </h2>
      <br>
      <form action="" method="post">
        <input type="text" placeholder="cheesyAdmins" name="hiddenUser" value="cheesyAdmins" hidden>
        <input type="text" placeholder="Username" name="username" value="{{
          request.form.username }}">
         <input type="password" placeholder="Password" name="password" value="{{
          request.form.password }}">
        <input class="btn btn-default" type="submit" value="Login">
      </form>
      {% if error %}
        <p class="error"><strong>Error:</strong> {{ error }}
      {% endif %}
    </div>
  </body>
</html>
```

As you can see you need to use the username cheesyAdmins and a simple "SQLi", now funny enough this is even harder than a normal SQLi because our system does not respond like a real one would making debugging nearly impossible.

```bash
app.route('/JumbledMess', methods=['GET'])
def home():
        return "flag{fs932jfsd8xc6z}"

# Route for handling the login page logic
@app.route('/login', methods=['GET', 'POST'])
def login():
    error = ""
    if request.method == 'POST':
        if "'" in request.form['password']:
                error += "SQL Error: Invalid query<br>"

        if request.form['username'] == 'cheesyAdmins' and "' or 1=1" in request.form['password'].lower():
            return redirect(url_for('home'))
        else:
            error += 'Invalid Credentials. Please try again.'
    return render_template('login.html', error=error)

app.run(host="0.0.0.0",port="5007")
```

This is where the cheating really begins because as you can the user never really gets updated. The server also returns some arguments and actually shows you exactly what parameters are required which you would need a swagger for IRL but this is just the beginning of our easy APIs. 

```bash
app.run(host="0.0.0.0",port="5007")
```

Combine all of this into one script and it will start up when we ask it, one last thing i want to highlight here is the host="0.0.0.0" argument. We do not need this but if we do not give it this argument, the API will only be accesible from localhost. The default port for flask will be 5000 but we told our script to run on port 5007 so make sure nothing else is running on that port.

Combine all of that into a file and we are ready to go.

```bash
python trainingLogin.py
```

replace [training](http://training1.py)Login.py with whatever you named your file.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fdb30235-7b38-4115-a7ea-dbb20a083b37/Untitled.png)

The first thing you will see is a login system, as this is a SQLi excercise you will have to try some inputs but you can always cheat and look at how the system was built ðŸ˜‚

We don't really need tools here as we can fuzz this by hand, but SQLmap would be a good addition i think.

# Extra: Full code

```python
import flask
from flask import Flask, render_template, redirect, url_for, request

app = flask.Flask(__name__)
app.config["DEBUG"] = True

@app.route('/JumbledMess', methods=['GET'])
def home():
        return "flag{fs932jfsd8xc6z}"

# Route for handling the login page logic
@app.route('/login', methods=['GET', 'POST'])
def login():
    error = ""
    if request.method == 'POST':
        if "'" in request.form['password']:
                error += "SQL Error: Invalid query<br>"

        if request.form['username'] == 'cheesyAdmins' and "' or 1=1" in request.form['password'].lower():
            return redirect(url_for('home'))
        else:
            error += 'Invalid Credentials. Please try again.'
    return render_template('login.html', error=error)

app.run(host="0.0.0.0",port="5007")
```

```html
<html>
  <head>
    <title>Flask Intro - login page</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link href="static/bootstrap.min.css" rel="stylesheet" media="screen">
  </head>
  <body>
    <div class="container">
      <h1>Please login</h1>
      <h2>The username can be found somewhere here on the page. The password? I don't know :D </h2>
      <br>
      <form action="" method="post">
        <input type="text" placeholder="cheesyAdmins" name="hiddenUser" value="cheesyAdmins" hidden>
        <input type="text" placeholder="Username" name="username" value="{{
          request.form.username }}">
         <input type="password" placeholder="Password" name="password" value="{{
          request.form.password }}">
        <input class="btn btn-default" type="submit" value="Login">
      </form>
      {% if error %}
        <p class="error"><strong>Error:</strong> {{ error }}
      {% endif %}
    </div>
  </body>
</html>
```
