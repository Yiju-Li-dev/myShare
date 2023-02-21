# Flask App Design

In a simple Flask app, we could define the app and its routes in the `applications/__init__.py` file(a.k.a., the entrance file):

**`applications/__init__.py`**
```python
from flask import Flask

app = Flask(__name__)
from application import routes
```
**`applications/routes.py`**
```python
# -*- coding: utf-8 -*-

from application import app
from flask import request
from flask import jsonify
from flask import render_template
import base64
import io
from k_means2 import get_colors



@app.route("/")
def index():
    return render_template("index.html", index=True )


@app.route("/", methods=['POST'])
def predict():
    message = request.get_json(force=True)
    encoded = message['image']
    decoded = base64.b64decode(encoded)
    image = io.BytesIO(decoded)
 
    k = int(message['name'])

    prediction = get_colors(image, k)
    print("Exit")
    
    keys = ['1', '2', '3', '4', '5', '6' ,'7', '8', '9']
   

    response = {}
    for i in range(len(prediction)):
        response[keys[i]] = prediction[i]
    
    return jsonify(response)
```
Where the first route renders the index page and the second route accepts a POST request with JSON data.
- [Rendering pages](rendering)
- [POST request: K_mean on server](post-request)