# **MongoDB Golang Project Documentation**
## **Project Overview**
This project demonstrates a simple web API built using Golang with MongoDB as the database. It uses ``httprouter`` for routing and ``mgo.v2`` for MongoDB integration. The project implements basic CRUD operations on a ``User`` resource, enabling clients to create, retrieve, and delete users via HTTP requests.

## **Project Structure**
The project is organized as follows:
```
├── controllers
│   └── user.go           # Controller logic for user-related operations
├── models
│   └── user.go           # User model structure
├── main.go               # Main application logic and routing
├── go.mod                # Dependency management (Golang modules)
└── go.sum                # Checksums for module dependencies
```
- **``controllers/``**: This folder contains the controllers that manage HTTP requests and handle the business logic.

  - ``user.go``: Defines the ``UserController``, responsible for handling CRUD operations on users.
- **``models/``**: Contains the models that represent data structures used within the application.

  - ``user.go``: Defines the ``User`` model.
- **``main.go``**: The entry point of the application, where the server is initialized, routes are defined, and MongoDB connection is established.

- ``go.mod`` and ``go.sum``: These files handle the Go module system and manage dependencies for the project.

### **Main.go Overview**
The ``main.go`` file is the entry point of the project. It initializes the HTTP server and sets up routes to handle user-related operations.

**The main functions**:
- **Router Setup**: Using ``httprouter``, routes are configured for GET, POST, and DELETE requests.
- **MongoDB Session**: The ``getSession()`` function establishes a connection to MongoDB.
- **Server Start**: The server listens on ``localhost:9000``.

Basic code structure:

```go
package main

import (
	"net/http"
	"github.com/julienschmidt/httprouter"
	"gopkg.in/mgo.v2"
	"github.com/MikeOnBoard/mongoGOlang/controllers"
)

func main() {
	r := httprouter.New()
	uc := controllers.NewUserController(getSession())

	r.GET("/user/:id", uc.GetUser)
	r.POST("/user", uc.CreateUser)
	r.DELETE("/user/:id", uc.DeleteUser)

	http.ListenAndServe("localhost:9000", r)
}

func getSession() *mgo.Session {
	s, err := mgo.Dial("mongodb://localhost:27017")
	if err != nil {
		panic(err)
	}
	return s
}
```
### Controllers (UserController)
The ``controllers/user.go`` file contains the logic for handling user-related HTTP requests. It includes the following methods in the ``UserController`` struct:

1. **GetUser**: Retrieves a user from the MongoDB database based on the provided ID.
2. **CreateUser**: Accepts a JSON request body to create a new user.
3. **DeleteUser**: Deletes a user from the database by ID.

These methods interact with the ``User`` model and the MongoDB database.

```go
type UserController struct {
	session *mgo.Session
}

func NewUserController(s *mgo.Session) *UserController {
	return &UserController{s}
}
```
The controller methods manage request parsing, database interaction, and response formatting.

### Models (User Model)
The ``models/user.go`` file defines the structure of the ``User`` model. The ``User`` struct mirrors the MongoDB document structure and uses ``bson`` and ``json`` tags for serialization between the database and the API.

```go
package models

import "gopkg.in/mgo.v2/bson"

type User struct {
	Id     bson.ObjectId `json:"id" bson:"_id"`
	Name   string        `json:"name" bson:"name"`
	Gender string        `json:"gender" bson:"gender"`
	Age    int           `json:"age" bson:"age"`
}
```
### **go.mod and go.sum**
- **``go.mod``**: Defines the Go module and its dependencies. It includes the modules required for ``httprouter`` and ``mgo.v2``.

Example content:

```go
module github.com/MikeOnBoard/mongoGOlang

go 1.16

require (
  github.com/julienschmidt/httprouter v1.3.0
  gopkg.in/mgo.v2 v2.0.0-20180705113604-9856a29383ce
)
```
- **``go.sum``**: Contains checksums for the Go modules to ensure package integrity.

### API Endpoints
This project exposes three main API endpoints for interacting with user data:

- **GET /user/**: Retrieves a user by their unique ID.

  - **Request**: GET request with user ID as a URL parameter.
  - **Response**: JSON representation of the user object.
  - **Status Codes**:
    - ``200 OK``: User found.
    - ``404 Not Found``: Invalid or non-existent user ID.
- **POST /user**: Creates a new user.

  - **Request**: POST request with user details (``name``, ``gender``, ``age``) in the request body.
  - **Response**: JSON representation of the created user.
  - **Status Codes**:
    - ``201 Created``: User successfully created.
- **DELETE /user/**: Deletes a user by their ID.

  - **Request**: DELETE request with user ID as a URL parameter.
  - **Response**: Confirmation message upon successful deletion.
  - **Status Codes**:
    - ``200 OK``: User successfully deleted.
    - ``404 Not Found``: Invalid or non-existent user ID.
## Conclusion
This project provides a simple yet functional RESTful API using Golang and MongoDB, demonstrating the basic implementation of CRUD operations for user management. It is well-structured with clear separation of concerns across controllers, models, and the main entry point, making it scalable and easy to maintain for future enhancements.
