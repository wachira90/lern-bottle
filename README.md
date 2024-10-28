# lerning bottle

Step-by-step guide on how to integrate Swagger with a simple Bottle application using the `swagger-ui` tool:

### Step 1: Install the required dependencies

First, install the required libraries:

```bash
pip install bottle
pip install swagger-ui-bundle
```

### Step 2: Create a basic Bottle application

Create a simple Bottle application in a file named `app.py`:

```python
from bottle import Bottle, run, request, response

app = Bottle()

# Basic route
@app.route('/hello', method='GET')
def hello():
    return {"message": "Hello, world!"}

# Another route with parameters
@app.route('/greet/<name>', method='GET')
def greet(name):
    return {"message": f"Hello, {name}!"}

if __name__ == "__main__":
    run(app, host='localhost', port=8080)
```

### Step 3: Set up Swagger configuration

To integrate Swagger, we need to add a Swagger definition (in JSON format) and serve it from the Bottle app.

Add the following route to serve the Swagger JSON:

```python
swagger_info = {
    "swagger": "2.0",
    "info": {
        "version": "1.0.0",
        "title": "Simple API",
        "description": "A simple API to demonstrate Swagger with Bottle"
    },
    "host": "localhost:8080",
    "basePath": "/",
    "schemes": ["http"],
    "paths": {
        "/hello": {
            "get": {
                "summary": "Returns a greeting",
                "responses": {
                    "200": {
                        "description": "Successful operation",
                        "schema": {
                            "type": "object",
                            "properties": {
                                "message": {
                                    "type": "string"
                                }
                            }
                        }
                    }
                }
            }
        },
        "/greet/{name}": {
            "get": {
                "summary": "Greet a user by name",
                "parameters": [
                    {
                        "name": "name",
                        "in": "path",
                        "required": True,
                        "type": "string"
                    }
                ],
                "responses": {
                    "200": {
                        "description": "Successful operation",
                        "schema": {
                            "type": "object",
                            "properties": {
                                "message": {
                                    "type": "string"
                                }
                            }
                        }
                    }
                }
            }
        }
    }
}

@app.route('/swagger.json')
def swagger_json():
    response.content_type = 'application/json'
    return swagger_info
```

### Step 4: Serve Swagger UI

Next, you need to serve Swagger UI using the `swagger-ui-bundle` package. You can do this by creating a static file route that serves the Swagger UI.

Add this route to your Bottle app:

```python
from swagger_ui_bundle import swagger_ui_3_path
from bottle import static_file

@app.route('/swagger-ui/<filepath:path>')
def swagger_ui(filepath):
    return static_file(filepath, root=swagger_ui_3_path)
```

### Step 5: Add Swagger UI index page

Create a custom HTML page to display the Swagger UI and load your API definition from `/swagger.json`.

Add the following route:

```python
@app.route('/swagger')
def swagger_ui_index():
    return '''
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Swagger UI</title>
        <link href="/swagger-ui/swagger-ui.css" rel="stylesheet">
    </head>
    <body>
        <div id="swagger-ui"></div>
        <script src="/swagger-ui/swagger-ui-bundle.js"></script>
        <script>
        const ui = SwaggerUIBundle({
            url: "/swagger.json",
            dom_id: '#swagger-ui',
        });
        </script>
    </body>
    </html>
    '''
```

### Step 6: Run the Bottle application

Your `app.py` should now look like this:

```python
from bottle import Bottle, run, request, response, static_file
from swagger_ui_bundle import swagger_ui_3_path

app = Bottle()

# Basic route
@app.route('/hello', method='GET')
def hello():
    return {"message": "Hello, world!"}

# Another route with parameters
@app.route('/greet/<name>', method='GET')
def greet(name):
    return {"message": f"Hello, {name}!"}

# Swagger JSON definition
swagger_info = {
    "swagger": "2.0",
    "info": {
        "version": "1.0.0",
        "title": "Simple API",
        "description": "A simple API to demonstrate Swagger with Bottle"
    },
    "host": "localhost:8080",
    "basePath": "/",
    "schemes": ["http"],
    "paths": {
        "/hello": {
            "get": {
                "summary": "Returns a greeting",
                "responses": {
                    "200": {
                        "description": "Successful operation",
                        "schema": {
                            "type": "object",
                            "properties": {
                                "message": {
                                    "type": "string"
                                }
                            }
                        }
                    }
                }
            }
        },
        "/greet/{name}": {
            "get": {
                "summary": "Greet a user by name",
                "parameters": [
                    {
                        "name": "name",
                        "in": "path",
                        "required": True,
                        "type": "string"
                    }
                ],
                "responses": {
                    "200": {
                        "description": "Successful operation",
                        "schema": {
                            "type": "object",
                            "properties": {
                                "message": {
                                    "type": "string"
                                }
                            }
                        }
                    }
                }
            }
        }
    }
}

@app.route('/swagger.json')
def swagger_json():
    response.content_type = 'application/json'
    return swagger_info

# Serve Swagger UI static files
@app.route('/swagger-ui/<filepath:path>')
def swagger_ui(filepath):
    return static_file(filepath, root=swagger_ui_3_path)

# Swagger UI page
@app.route('/swagger')
def swagger_ui_index():
    return '''
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Swagger UI</title>
        <link href="/swagger-ui/swagger-ui.css" rel="stylesheet">
    </head>
    <body>
        <div id="swagger-ui"></div>
        <script src="/swagger-ui/swagger-ui-bundle.js"></script>
        <script>
        const ui = SwaggerUIBundle({
            url: "/swagger.json",
            dom_id: '#swagger-ui',
        });
        </script>
    </body>
    </html>
    '''

if __name__ == "__main__":
    run(app, host='localhost', port=8080)
```

### Step 7: Access Swagger UI

1. Run your Bottle app:

   ```bash
   python app.py
   ```

2. Open a browser and go to `http://localhost:8080/swagger`.

You should now see the Swagger UI interface where you can interact with the defined API endpoints.

That's it! You've integrated Swagger with a simple Bottle application.
