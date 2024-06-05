0x01. Basic authentication
Background Context
In this project, you will learn what the authentication process means and implement a Basic Authentication on a simple API.
In the industry, you should not implement your own Basic authentication system and use a module or framework that doing it for you (like in Python-Flask: Flask-HTTPAuth). Here, for the learning purpose, we will walk through each step of this mechanism to understand it by doing.
Resources to Read or watch:
    •	REST API Authentication Mechanisms
    •	Base64 in Python
    •	HTTP header Authorization
    •	Flask
    •	Base64 - concept
General Learning Objectives
At the end of this project, you are expected to be able to explain to anyone, without the help of Google:
    •	What authentication means
    •	What Base64 is
    •	How to encode a string in Base64
    •	What Basic authentication means
    •	How to send the Authorization header
Requirements of the Python Scripts
    •	All your files will be interpreted/compiled on Ubuntu 18.04 LTS using python3 (version 3.7)
    •	All your files should end with a new line
    •	The first line of all your files should be exactly #!/usr/bin/env python3
    •	A README.md file, at the root of the folder of the project, is mandatory
    •	Your code should use the pycodestyle style (version 2.5)
    •	All your files must be executable
    •	The length of your files will be tested using wc
    •	All your modules should have a documentation (python3 -c 'print(__import__("my_module").__doc__)')
    •	All your classes should have a documentation (python3 -c 'print(__import__("my_module").MyClass.__doc__)')
    •	All your functions (inside and outside a class) should have a documentation (python3 -c 'print(__import__("my_module").my_function.__doc__)' and python3 -c 'print(__import__("my_module").MyClass.my_function.__doc__)')
    •	A documentation is not a simple word, it’s a real sentence explaining what’s the purpose of the module, class or method (the length of it will be verified)

Tasks

0. Simple-basic-API
    Download and start your project from this archive.zip
    In this archive, you will find a simple API with one model: User. Storage of these users is done via a serialization/deserialization in files.
    Setup and start server
    Use the API (in another tab or in your browser)
    Repo:
        •	GitHub repository: alx-backend-user-data
        •	Directory: 0x01-Basic_authentication
1. Error handler: Unauthorized
    What the HTTP status code for a request unauthorized? 401 of course!
    Edit api/v1/app.py:
    •	Add a new error handler for this status code, the response must be:
        o	a JSON: {"error": "Unauthorized"}
        o	status code 401
        o	you must use jsonify from Flask
    For testing this new error handler, add a new endpoint in api/v1/views/index.py:
    •	Route: GET /api/v1/unauthorized
    •	This endpoint must raise a 401 error by using abort - Custom Error Pages 
    By calling abort(401), the error handler for 401 will be executed.
    Repo:
    •	File: api/v1/app.py, api/v1/views/index.py
2. Error handler: Forbidden
What the HTTP status code for a request where the user is authenticate but not allowed to access to a resource? 403 of course!
Edit api/v1/app.py:
    •	Add a new error handler for this status code, the response must be:
        o	a JSON: {"error": "Forbidden"}
        o	status code 403
        o	you must use jsonify from Flask
For testing this new error handler, add a new endpoint in api/v1/views/index.py:
    •	Route: GET /api/v1/forbidden
    •	This endpoint must raise a 403 error by using abort - Custom Error Pages
By calling abort(403), the error handler for 403 will be executed.
Repo:
    •	GitHub repository: alx-backend-user-data
    •	Directory: 0x01-Basic_authentication
    •	File: api/v1/app.py, api/v1/views/index.py
3. Auth class
Now you will create a class to manage the API authentication.
    •	Create a folder api/v1/auth
    •	Create an empty file api/v1/auth/__init__.py
    •	Create the class Auth:
        o	in the file api/v1/auth/auth.py
        o	import request from flask
        o	class name Auth
        o	public method def require_auth(self, path: str, excluded_paths: List[str]) -> bool: that returns False - path and excluded_paths will be used later, now, you don’t need to take care of them
        o	public method def authorization_header(self, request=None) -> str: that returns None - request will be the Flask request object
        o	public method def current_user(self, request=None) -> TypeVar('User'): that returns None - request will be the Flask request object
This class is the template for all authentication system you will implement.
Repo:
    •	GitHub repository: alx-backend-user-data
    •	Directory: 0x01-Basic_authentication
    •	File: api/v1/auth, api/v1/auth/__init__.py, api/v1/auth/auth.py
4. Define which routes don't need authentication
Update the method def require_auth(self, path: str, excluded_paths: List[str]) -> bool: in Auth that returns True if the path is not in the list of strings excluded_paths:
    •	Returns True if path is None
    •	Returns True if excluded_paths is None or empty
    •	Returns False if path is in excluded_paths
    •	You can assume excluded_paths contains string path always ending by a /
    •	This method must be slash tolerant: path=/api/v1/status and path=/api/v1/status/ must be returned False if excluded_paths contains /api/v1/status/
Repo:
    •	GitHub repository: alx-backend-user-data
    •	Directory: 0x01-Basic_authentication
    •	File: api/v1/auth/auth.py
5. Request validation!
Now you will validate all requests to secure the API:
Update the method def authorization_header(self, request=None) -> str: in api/v1/auth/auth.py:
    •	If request is None, returns None
    •	If request doesn’t contain the header key Authorization, returns None
    •	Otherwise, return the value of the header request Authorization
    Update the file api/v1/app.py:
    •	Create a variable auth initialized to None after the CORS definition
    •	Based on the environment variable AUTH_TYPE, load and assign the right instance of authentication to auth
        o	if auth:
            	import Auth from api.v1.auth.auth
            	create an instance of Auth and assign it to the variable auth
Now the biggest piece is the filtering of each request. For that you will use the Flask method before_request
    •	Add a method in api/v1/app.py to handler before_request
        o	if auth is None, do nothing
        o	if request.path is not part of this list ['/api/v1/status/', '/api/v1/unauthorized/', '/api/v1/forbidden/'], do nothing - you must use the method require_auth from the auth instance
        o	if auth.authorization_header(request) returns None, raise the error 401 - you must use abort
        o	if auth.current_user(request) returns None, raise the error 403 - you must use abort
Repo:
    •	GitHub repository: alx-backend-user-data
    •	Directory: 0x01-Basic_authentication
    •	File: api/v1/app.py, api/v1/auth/auth.py
6. Basic auth
Create a class BasicAuth that inherits from Auth. For the moment this class will be empty.
Update api/v1/app.py for using BasicAuth class instead of Auth depending of the value of the environment variable AUTH_TYPE, If AUTH_TYPE is equal to basic_auth:
    •	import BasicAuth from api.v1.auth.basic_auth
    •	create an instance of BasicAuth and assign it to the variable auth
Otherwise, keep the previous mechanism with auth an instance of Auth.
Repo:
    •	GitHub repository: alx-backend-user-data
    •	Directory: 0x01-Basic_authentication
    •	File: api/v1/app.py, api/v1/auth/basic_auth.py
7. Basic - Base64 part
Add the method def extract_base64_authorization_header(self, authorization_header: str) -> str: in the class BasicAuth that returns the Base64 part of the Authorization header for a Basic Authentication:
    •	Return None if authorization_header is None
    •	Return None if authorization_header is not a string
    •	Return None if authorization_header doesn’t start by Basic (with a space at the end)
    •	Otherwise, return the value after Basic (after the space)
    •	You can assume authorization_header contains only one Basic
Repo:
    •	GitHub repository: alx-backend-user-data
    •	Directory: 0x01-Basic_authentication
    •	File: api/v1/auth/basic_auth.py
8. Basic - Base64 decode
Add the method def decode_base64_authorization_header(self, base64_authorization_header: str) -> str: in the class BasicAuth that returns the decoded value of a Base64 string base64_authorization_header:
    •	Return None if base64_authorization_header is None
    •	Return None if base64_authorization_header is not a string
    •	Return None if base64_authorization_header is not a valid Base64 - you can use try/except
    •	Otherwise, return the decoded value as UTF8 string - you can use decode('utf-8')
Repo:
    •	GitHub repository: alx-backend-user-data
    •	Directory: 0x01-Basic_authentication
    •	File: api/v1/auth/basic_auth.py
9. Basic - User credentials
Add the method def extract_user_credentials(self, decoded_base64_authorization_header: str) -> (str, str) in the class BasicAuth that returns the user email and password from the Base64 decoded value.
This method must return 2 values
    •	Return None, None if decoded_base64_authorization_header is None
    •	Return None, None if decoded_base64_authorization_header is not a string
    •	Return None, None if decoded_base64_authorization_header doesn’t contain :
    •	Otherwise, return the user email and the user password - these 2 values must be separated by a :
    •	You can assume decoded_base64_authorization_header will contain only one :
Repo:
    •	GitHub repository: alx-backend-user-data
    •	Directory: 0x01-Basic_authentication
    •	File: api/v1/auth/basic_auth.py
10. Basic - User object
Add the method def user_object_from_credentials(self, user_email: str, user_pwd: str) -> TypeVar('User'): in the class BasicAuth that returns the User instance based on his email and password.
    •	Return None if user_email is None or not a string
    •	Return None if user_pwd is None or not a string
    •	Return None if your database (file) doesn’t contain any User instance with email equal to user_email - you should use the class method search of the User to lookup the list of users based on their email. Don’t forget to test all cases: “what if there is no user in DB?”, etc.
    •	Return None if user_pwd is not the password of the User instance found - you must use the method is_valid_password of User
    •	Otherwise, return the User instance
Repo:
    •	GitHub repository: alx-backend-user-data
    •	Directory: 0x01-Basic_authentication
    •	File: api/v1/auth/basic_auth.py
11. Basic - Overload current_user - and BOOM!
Now, you have all pieces for having a complete Basic authentication.
Add the method def current_user(self, request=None) -> TypeVar('User') in the class BasicAuth that overloads Auth and retrieves the User instance for a request:
    •	You must use authorization_header
    •	You must use extract_base64_authorization_header
    •	You must use decode_base64_authorization_header
    •	You must use extract_user_credentials
    •	You must use user_object_from_credentials
    •	With this update, now your API is fully protected by a Basic Authentication. Enjoy!
    •	In the first terminal:
Repo:
    •	GitHub repository: alx-backend-user-data
    •	Directory: 0x01-Basic_authentication
    •	File: api/v1/auth/basic_auth.py
