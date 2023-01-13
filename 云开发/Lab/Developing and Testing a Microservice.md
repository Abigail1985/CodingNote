
## 1 Introduction

Until now, you have been working on the "operations" part of DevOps by creating and configuring OpenStack servers and by creating Docker containers. Starting with this lab, we are moving to the "development" part.

We are going to consider a simple photo sharing application, allowing its users to upload photos and share them with each other. The implementation of the application is based on microservices. During this lab, you will learn how to develop and test three of them (`photographer`, `photo`, and `tags`):

- the `photographer` service manages the photographers and their attributes: name, first name, display name and interests. It offers a REST API.
- the `photo` service manages the photos and some attributes: location, title, tags, etc. It offers a REST API.
- the `tags` service analyzes a photo and automatically suggests a certain number of tags. This service is intended to be used by the `photo` service. It exposes a gRPC interface.

![fig-photoapp-ms.jpg](http://www.cloud.rennes.enst-bretagne.fr/fig-photoapp-ms.jpg)

For the time being, we will use a command line REST client, called **curl** or a **Swagger** GUI to interact with the microservices. Later, when you build the final application, you will discover how to integrate these microservices with a web client.

## 2 Downloading the Code

The `photographer` service and the `photo` service are two of the microservices of your photo sharing application. The `photographer` service is in charge of managing (and storing) all the information about the photographers. The `photo` service store photos (as jpeg files) and associated metadata. These services are already implemented.

Execute the following commands to download the source code of the services in the `/app` directory of your git repository

```
> cd location_of_your_git_repo
> mkdir -p app/photographer-service
> mkdir -p app/photo-service
> cd app/photographer-service
> wget -q -l1 -r -nd --reject="index.*" http://www.cloud.rennes.enst-bretagne.fr/files/rest-lab/photographer-service/
> cd ../photo-service
> wget -q -l1 -r -nd --reject="index.*" http://www.cloud.rennes.enst-bretagne.fr/files/rest-lab/photo-service/
```

# 3 The `photographer` Service

The `photographer` service exposes a **REST** API. It is implemented with the Python **FastAPI** framework. The **pytest** framework (seehttps://docs.pytest.org/en/latest/) is used to implement functional testing of the service. The `Photographer` service relies on a Mongo database to store the attributes of a photographer.

### 3.1 Files

In the `app/photographer-service` directory of your repository, you will find all the files related to the `Photographer`microservice:

- `photographer_service.py` contains the definition of the python functions serving the HTTP requests to the endpoints of the `Photographer` service.
- `models.py` is a helper module for the `photographer_service.py` module

- `test_photographer.py` contains the definition of the functions for testing the service with **pytest**.

- `conftest.py` contains fixtures used by **pytest**.

### 3.2 REST API

The endpoints and the HTTP methods associated to these endpoints are the following:

- `/photographers`
  - `POST` to create a new photographer. It returns the URI of the photographer in the HTTP `Location` header field
  - `GET`, to obtain a `Photographers` object (array of `PhotographerDigest` and a boolean indicating if there are more photographers to return).

- `/photographer/{display_name}`
	- `PUT`, to update the photographer attributes of a photographer identified by `display_name`.
	- `GET` to obtain the JSON object mapping photographer attributes names to their values for a given photographer (identified by `display_name`).

### 3.3 Mongo

The `Photographer` service stores photographer attributes in a Mongo database. These attributes are: `display_name`, `first_name`, `last_name`, `interests`, `_id`. The `models.py` module defines the `Photographer` as a model class for storing the attributes of a photographer in a Mongo document. The `id` is not defined because we will let Mongo choose this identifier for us. Please, read the documentation for Beanie (the object modeling package that we are using) at the following address: https://roman-right.github.io/beanie/


[[用FastAPI、MongoDB和Beanie构建一个CRUD应用程序]]


### 3.4 Starting and Discovering the Service



The `Photographer` service relies on a Mongo Database. Before running the service, a Mongo database must be somehow available. You may either install and run Mongo on your local VM or run a Mongo instance in a docker container (the easiest and recommended way). The following instructions consider that you run Mongo in a docker container.

To start a Mongo instance in a container, please use the following command:

```
> docker run --name mongo-service -d mongo
```

The `photographer_service.py` module contains the definition of the python functions serving the HTTP requests to the endpoints of the `Photographer` service. The `photographer` service can be started either 1) directly on your local VM or 2) in a Docker container running on top of your local VM. As for Mongo, we will choose the docker solution. Running the `photographer` service in a docker container requires to build the docker image. The `Dockerfile` is provided.

The connection to the `mongo` database is delegated to an event handler function decorated with `@app.on_event("startup")`.

Go to the `app/photographer-service` directory and then, build the docker image and run the service using the following commands:

```
> docker build -t photographer .
> docker run -p 8000:80 --env MONGO_HOST=mongo_ip --name photographer-service photographer
```


如何查看容器的IP地址：[[Docker#3.2 Docker Inspect and Docker Logs]]

The `--env MONGO_HOST` option is used to give the IP of the mongo service (running in a container) to the `photographer` service. The `-p 8000:80` option is to publish the container port 80 to port 8000 on the docker host (your local VM). Note that if you don't give the IP of your Mongo container in the `docker run` command, the `photographer` service will try to connect to a Mongo service whose IP is specified in the Dockerfile. This may be the right one… or not.

Start a Web navigator and type the following address http://127.0.0.1:8000/docs in the address bar. Experiment some of the endpoints of the `photographer` service API exposed by the Swagger graphical interface.

### 3.5 Testing the Photographer Service

[[pytest的一些配置文件]]
[[pytest.usefixtures用法]]

You are going to use the **pytest** framework to the test the `photographer` service. The test functions are in the `test_photographer.py` file. 

Fixtures are in the `conftest.py` file. **Fixtures set up and release resources before and after the execution of tests.** 

For example, the `initDB` fixture is executed to establish a connection to the database before executing a test function. 
The `clearPhotographers` fixture is also executed before running every test function; here, the author of the tests choose to delete all documents from the collection before running a test.

The test functions for the `Photographer` service rely on a Mongo Database. Consequently, before running the tests, a Mongo database must be started. In the following, we use a docker container to start a Mongo instance.

Start a Mongo instance in a container using the following command.

```
> docker run --name mongo-service-test -d mongo
```

You will now build a docker image to execute the tests for the `Photographer` service in a docker container. Consider the following Dockerfile (name is `TestingDockerfile` to distinguish it from `Dockerfile`):

```
FROM python:3.9
RUN python3 -m pip install --upgrade pip
RUN pip3 install --no-cache-dir --trusted-host pypi.python.org pytest pytest_asyncio beanie httpx fastapi[all] Pillow protobuf grpcio grpcio-tools requests
```

As you may have noticed, there is nothing specific to the `Photographer` service in this Dockerfile. Only python packages required for running tests for a service relying on the `fastapi` and `beanie` frameworks are installed in the docker image. You will tag the image with a generic name and you will use this image to test all your microservices.

Build the image using the following command.

```
> docker build -f TestingDockerfile -t photoapptest .
```

Executing `pytest` will run all files of the form `test_*.py` or `*_test.py` in the current directory and its subdirectories.

Launch the container based on your image by using the following command.

```
> docker run --env MONGO_HOST=mongo_ip -v $(pwd):/app -w /app photoapptest pytest
```

Let's analyze the above command:

- `-v` is used to mount the directory containing the service on the `/app` directory inside the container
- `--env MONGO_HOST` option is used to give the IP of the mongo service used for the test
- `-w` means that we set the working directory to `/app`
- `photoapptest` is the name of the image
- `pytest` is the command to run the test

The output of the command should give you four tests with status `PASSED`.

That's all very well but it is a bit time consuming to manually run tests (imagine if you had hundred of microservices to test!). As already mentioned in previous labs, one of the key ideas behind DevOps is to automate all what can be automated. That's what we will explore in another lab dedicated to **Continuous Integration/Delivery/Deployment**

### 3.6 Deleting a Photographer

Your next goal is to add a `DELETE` method to the `/photographer/{display_name}` endpoint for deleting a photographer.

Modify the `photographer_service.py` according this requirement.

In Section [3.4](http://www.cloud.rennes.enst-bretagne.fr/rest-lab.html#orgb82d410), you learnt how to start the `photographer` service in a Docker container. Of course, you can apply this method here, i.e. build a Docker image with the python code of your service and then run the container from the image. However, even if the work to be done is limited, it is likely that you will go through several iterations before you succeed and that you will have to build and re-build the Docker image. This is a bit tedious. An alternative is to proceed as you did for the test (see Section [3.5](http://www.cloud.rennes.enst-bretagne.fr/rest-lab.html#org92e8fb9)), i.e. to mount the directory containing the file of your service when you run the container. Of course, the server must also run with live auto-reload so that it re-starts automatically at every code change. For this to work, you need to add the `--reload` option to the `uvicorn` command in the Dockerfile you use for the service.

```
> cd location_of_your_git_repo/app/photographer-service
> docker run -p 8000:80 --env MONGO_HOST=mongo_ip -v $(pwd):/code --name photographer-service photographer
```

In this way, each time you modify the `photographer_service.py` file (or imported modules), the server will load the new version.

Experiment the `DELETE` method of the `Photographer` service by using the Swagger graphical interface

Add a new test method for the `DELETE` method in the `test_photographer.py` file ans use `pytest` to run it.

# 4 The `tags` Service

The role of the `tags` service is to analyze a photo and automatically suggest a certain number of tags. This service exposes a **gRPC**interface. Even though you do not need to be aware of this, in case you are wondering, the service is written in Java.

You will have the following items at your disposal:

- The **proto** file describing the **RPC** interface of the service.
- A Docker image with the code of the service.

In its current version, this service is extremely simple: it returns a random list of tags without analyzing the image. With a bit of luck, future versions of this service will use machine learning to suggest more pertinent tags.

### 4.1 Tags Proto File

As you now know, when using a RPC system, there is a contract between the client and server. With gRPC, this contract is a proto file that defines the interface of the service. Here is the interface of the `tags` service:

```
syntax = "proto3";

service Tags {
  rpc getTags (ImageRequest) returns (TagsReply) {}
}

message ImageRequest {
  bytes file = 1;
}

message TagsReply {
  repeated string tags = 1;
}
```

As you can see, this interface could hardly be simpler: only one procedure is defined: `getTags`. This procedure accepts as an input a message with the bytes of an image and returns another message with a sequence of strings, each string corresponding to a tag.

This file is available at: http://www.cloud.rennes.enst-bretagne.fr/files/rest-lab/tags-service/tags.proto
### 4.2 Docker Image for Tags Service

The Docker image containing the implementation of the `tags` service is available in the GitLab shared registry: `gitlab-devops.cloud.rennes.enst-bretagne.fr:4567/devops/shared/tags`

The port number exposed by the gRPC `tags` service is: `50051`.

Run the `tags` service in a Docker container on your local VM.

# 5 The `photo` Service

The `photo` service is also implemented with FastAPI and uses a Mongo database. Unlike the `photographer` service, it does not use `beanie` but `mongoengine` to access the database. Note that this is of course not a problem because both services are independent and they communicate with each other via a REST API. You should know that the `photo` service is a client of the `photographer` service and of the `tags` service. When a photo has to be added to a photographer's gallery, the `photo` service asks the `photographer` service to check the existence of the photographer and asks the `tags` service to suggest tags for the photo.

Deliberately, we don't detail the software architecture of this service (different from the `photo` service regarding the access to the Mongo database) in order not to confuse you. You may need to modify its code if the service you choose to develop is a server with respect to the `photo` service. If this is the case, however, you don't need to worry about using `mongoengine` instead of `beanie`.

### 5.1 Model

The `photo` service store photos (as jpeg files) and associated metadata. The metadata has the following fields (each field is optional except the author):

- `title` (string)
- `location` (string)
- `author` (string, corresponding to the photographer `display_name`)
- `comment` (string)
- `tags` (list of strings)

### 5.2 REST API

The `photo` service exposes a **REST** API. It is implemented using the Python **FastAPI** framework. It uses Mongo to persistently store data. The endpoints and the associated HTTP methods are:

- ```
  /gallery/{display_name}
  ```

  - `POST` to upload a new photo for the photographer identified by `display_name`. It returns the URI of the photo in the HTTP `Location` header field. The URI of the photo must be of the following form: `/photo/{display_name}/{photo_id}`, `photo_id` being a unique identifier (for the photographer identified by `display_name`).
  - `GET` to obtain a `Photos` object (array of `PhotoDigest` and a boolean indicating if there are more photos to return).

- ```
  /photo/{display_name}/{photo_id}
  ```

  - `GET` to obtain the photo identified by `photo_id` taken by the photographer identified by `display_name`

- ```
  /photo/{display_name}/{photo_id}/attributes
  ```

  - `GET` to obtain the attributes of the photo identified by `photo_id` taken by the photographer identified by `display_name`
  - `PUT`, to set the attributes the photo identified by `photo_id` taken by the photographer identified by `display_name`

The text above suggests that sending a photo and its attributes should be done in two requests:

- a first `POST` request to upload the photo (and only the photo).
- a second `PUT` request to set the attributes of the photo.

With the first request, we send the photo and retrieve a URI in the `Location` field of the HTTP response header, for example: `/photo/rdoisneau/3`. Thanks to this URI, we can then send a second request, `PUT` this time, to upload the photo's attributes: `/photo/rdoisneau/3/attributes`. The `/photo/rdoisneau/3` URI refers to the photo itself. The `/photo/rdoisneau/3/attributes` URI refers to the attributes of the photo.

### 5.3 Starting and Discovering the Service

Go to the `app/photo-service` directory and then, build the docker image and run the service using the following commands:

```
> docker build -t photo .
> docker run --env MONGO_HOST=mongo_ip --env TAGS_HOST=tasg_ip --env PHOTOGRAPHER_HOST=photographer_ip -p 8001:80 --name photo-service photo:latest
```

Start a Web navigator and type the following address http://127.0.0.1:8001/docs in the address bar. Experiment some of the endpoints of the `photo` service API exposed by the Swagger graphical interface.

# 6 Your service

Each group must implement an additional service adding a new feature to the Photo Sharing App. You must:

- Discuss the feature and corresponding service with the teaching staff.
- Write a short text document explaining the (REST or gRPC) API of your service.
- Discuss the proposed API with the teaching staff.
- Implement the service.

Author: Alberto Blanc, Jean-Pierre Le Narzul

Created: 2022-02-15 Tue 11:41

[Validate](https://validator.w3.org/check?uri=referer)