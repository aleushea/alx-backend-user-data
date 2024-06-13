0x03. User authentication service
Repo:
•	GitHub repository: alx-backend-user-data
•	Directory: 0x03-user_authentication_service
In the industry, you should not implement your own authentication system and use a module or framework that doing it for you (like in Python-Flask: Flask-User). Here, for the learning purpose, we will walk through each step of this mechanism to understand it by doing.
Resources to Read or watch:
•	Flask documentation
•	Requests module
•	HTTP status codes
Learning Objectives
At the end of this project, you are expected to be able to explain to anyone, without the help of Google:
•	How to declare API routes in a Flask app
•	How to get and set cookies
•	How to retrieve request form data
•	How to return various HTTP status codes
Requirements
•	Allowed editors: vi, vim, emacs
•	All your files will be interpreted/compiled on Ubuntu 18.04 LTS using python3 (version 3.7)
•	All your files should end with a new line
•	The first line of all your files should be exactly #!/usr/bin/env python3
•	A README.md file, at the root of the folder of the project, is mandatory
•	Your code should use the pycodestyle style (version 2.5)
•	You should use SQLAlchemy 1.3.x
•	All your files must be executable
•	The length of your files will be tested using wc
•	All your modules should have a documentation (python3 -c 'print(__import__("my_module").__doc__)')
•	All your classes should have a documentation (python3 -c 'print(__import__("my_module").MyClass.__doc__)')
•	All your functions (inside and outside a class) should have a documentation (python3 -c 'print(__import__("my_module").my_function.__doc__)' and python3 -c 'print(__import__("my_module").MyClass.my_function.__doc__)')
•	A documentation is not a simple word, it’s a real sentence explaining what’s the purpose of the module, class or method (the length of it will be verified)
•	All your functions should be type annotated
•	The flask app should only interact with Auth and never with DB directly.
•	Only public methods of Auth and DB should be used outside these classes
Setup
You will need to install bcrypt
pip3 install bcrypt
Tasks
0. User model
mandatory
In this task you will create a SQLAlchemy model named User for a database table named users (by using the mapping declaration of SQLAlchemy).

The model will have the following attributes:

•	id, the integer primary key
•	email, a non-nullable string
•	hashed_password, a non-nullable string
•	session_id, a nullable string
•	reset_token, a nullable string
File: user.py
  
1. create user
•	In this task, you will complete the DB class provided below to implement the add_user method.
•	Note that DB._session is a private property and hence should NEVER be used from outside the DB class.
•	Implement the add_user method, which has two required string arguments: email and hashed_password, and returns a User object. The method should save the user to the database. No validations are required at this stage.
•	File: db.py
  
2. Find user
•	In this task you will implement the DB.find_user_by method. This method takes in arbitrary keyword arguments and returns the first row found in the users table as filtered by the method’s input arguments. No validation of input arguments required at this point.
•	Make sure that SQLAlchemy’s NoResultFound and InvalidRequestError are raised when no results are found, or when wrong query arguments are passed, respectively.
Warning:
•	NoResultFound has been moved from sqlalchemy.orm.exc to sqlalchemy.exc between the version 1.3.x and 1.4.x of SQLAchemy - please make sure you are importing it from sqlalchemy.orm.exc
•	File: db.py
  
3. update user
•	In this task, you will implement the DB.update_user method that takes as argument a required user_id integer and arbitrary keyword arguments, and returns None.
•	The method will use find_user_by to locate the user to update, then will update the user’s attributes as passed in the method’s arguments then commit changes to the database.
•	If an argument that does not correspond to a user attribute is passed, raise a ValueError.
•	File: db.py
4. Hash password
•	In this task you will define a _hash_password method that takes in a password string arguments and returns bytes.
•	The returned bytes is a salted hash of the input password, hashed with bcrypt.hashpw.
•	File: auth.py
5. Register user
•	In this task, you will implement the Auth.register_user in the Auth class provided below:
•	from db import DB
•	File: auth.py
6. Basic Flask app
•	In this task, you will set up a basic Flask app.
•	Create a Flask app that has a single GET route ("/") and use flask.jsonify to return a JSON payload of the form:
•	File: app.py
7. Register user
•	In this task, you will implement the end-point to register a user. Define a users function that implements the POST /users route.
•	Import the Auth object and instantiate it at the root of the module as such:
•	File: app.py
8. Credentials validation
•	In this task, you will implement the Auth.valid_login method. It should expect email and password required arguments and return a boolean.
•	Try locating the user by email. If it exists, check the password with bcrypt.checkpw. If it matches return True. In any other case, return False.
•	File: auth.py
9. Generate UUIDs
•	In this task you will implement a _generate_uuid function in the auth module. The function should return a string representation of a new UUID. Use the uuid module.
•	Note that the method is private to the auth module and should NOT be used outside of it.
•	File: auth.py
10. Get session ID
•	In this task, you will implement the Auth.create_session method. It takes an email string argument and returns the session ID as a string.
•	The method should find the user corresponding to the email, generate a new UUID and store it in the database as the user’s session_id, then return the session ID.
•	Remember that only public methods of self._db can be used.
•	File: auth.py
11. Log in
•	In this task, you will implement a login function to respond to the POST /sessions route.
•	The request is expected to contain form data with "email" and a "password" fields.
•	If the login information is incorrect, use flask.abort to respond with a 401 HTTP status.
•	Otherwise, create a new session for the user, store it the session ID as a cookie with key "session_id" on the response and return a JSON payload of the form
•	{"email": "<user email>", "message": "logged in"}
•	File: app.py
12. Find user by session ID
•	In this task, you will implement the Auth.get_user_from_session_id method. It takes a single session_id string argument and returns the corresponding User or None.
•	If the session ID is None or no user is found, return None. Otherwise return the corresponding user.
•	Remember to only use public methods of self._db.
•	File: auth.py
13. Destroy session
•	In this task, you will implement Auth.destroy_session. The method takes a single user_id integer argument and returns None.
•	The method updates the corresponding user’s session ID to None.
•	Remember to only use public methods of self._db.

•	File: auth.py
14. Log out
•	In this task, you will implement a logout function to respond to the DELETE /sessions route.
•	The request is expected to contain the session ID as a cookie with key "session_id".
•	Find the user with the requested session ID. If the user exists destroy the session and redirect the user to GET /. If the user does not exist, respond with a 403 HTTP status.
•	File: app.py
15. User profile
•	In this task, you will implement a profile function to respond to the GET /profile route.
•	The request is expected to contain a session_id cookie. Use it to find the user. If the user exist, respond with a 200 HTTP status and the following JSON payload:
•	{"email": "<user email>"}
•	If the session ID is invalid or the user does not exist, respond with a 403 HTTP status.
•	File: app.py
16. Generate reset password token
•	In this task, you will implement the Auth.get_reset_password_token method. It take an email string argument and returns a string.
•	Find the user corresponding to the email. If the user does not exist, raise a ValueError exception. If it exists, generate a UUID and update the user’s reset_token database field. Return the token.
•	File: auth.py
17. Get reset password token
•	In this task, you will implement a get_reset_password_token function to respond to the POST /reset_password route.
•	The request is expected to contain form data with the "email" field.
•	If the email is not registered, respond with a 403 status code. Otherwise, generate a token and respond with a 200 HTTP status and the following JSON payload:
•	{"email": "<user email>", "reset_token": "<reset token>"}
•	File: app.py
18. Update password
•	In this task, you will implement the Auth.update_password method. It takes reset_token string argument and a password string argument and returns None.
•	Use the reset_token to find the corresponding user. If it does not exist, raise a ValueError exception.
•	Otherwise, hash the password and update the user’s hashed_password field with the new hashed password and the reset_token field to None.
•	File: auth.py
19. Update password end-point
•	In this task you will implement the update_password function in the app module to respond to the PUT /reset_password route.
•	The request is expected to contain form data with fields "email", "reset_token" and "new_password".
•	Update the password. If the token is invalid, catch the exception and respond with a 403 HTTP code.
•	If the token is valid, respond with a 200 HTTP code and the following JSON payload:
•	{"email": "<user email>", "message": "Password updated"}
20. End-to-end integration test
•	Start your app. Open a new terminal window.

•	Create a new module called main.py. Create one function for each of the following tasks. Use the requests module to query your web server for the corresponding end-point. Use assert to validate the response’s expected status code and payload (if any) for each task.
•	File: main.py

