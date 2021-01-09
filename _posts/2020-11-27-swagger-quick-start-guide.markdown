---
layout: post
title:  "Swagger quick start guide"
date:   2020-11-27 12:22:40 +0000
categories: swagger development documentation
---
![OpenAPI logo](/assets/images/posts/2020-11-27-swagger-quick-start-guide/header.png)

When working with microservices applications we sometimes find ourselves surrounded by many endpoints that we don’t know how they work. Sometimes we don’t even know that an endpoint exists. The solution to this goes around proper documentation.

OAS (OpenAPI Specification) is a language-agnostic standard to specify and document RESTful APIs. It is used all over the world by many companies like Twitter and Stripe. It offers an interface that allows both humans and computers to interact without having to clone a repository to look at what is available and how it works.

On top of OAS, we have Swagger. It is a set of open-source tools that allows easy design and documentation of APIs.

In this post, I’ll cover the basics of an OAS document by documenting a CRUD API and basic authentication, and the usage of Swagger UI to interact with it.

# Let’s get started

OAS is itself a valid JSON object. It could be represented by either JSON or YAML format, and the specification document could rely on single or multiple files.

The supported data types follow the JSON Schema Specification, they are integer, number, and string. null is not a supported data type, but you can use nullable: true to specify that a given parameter is nullable.

The structure of the file requires some fields to be filled. A basic structure contains the version of the OAS, metadata about the API, and the paths and operations for the API. The complete specification can be found on the [official page](https://swagger.io/specification/).

As I mentioned, we’ll be using Swagger UI to visualize our documentation. It is nothing more than a friendly interface that interprets an OAS. You could use many different visualization apps on top of OAS.

# Building our first specification

Imagine that you’re developing an API to a tracking system that monitors order delivery in some e-commerce. Your product manager came up with the following specification:

    1 — Every request must authenticate using an API key provided in the response of the authentication endpoint
    2 — The API should provide the following operations:
    2.1 — Add a tracking number
    2.2 — Get all tracking numbers
    2.3 — Get all tracking numbers by the delivery status
    2.4 — Get details of a given tracking number

Before start coding, we usually design our API for easy usage of our users. To do that will begin writing a specification that is readable by Swagger UI to let it be publicly available to your users.

The first step is to specify the metadata of your API. You can add some extra information like the host and base path of your API. This will be enough to generate general information about the service you’ll be providing to your users. Save this file as tracking-api.yml and load it to Swagger UI using a docker image:

```yaml
swagger: "2.0" # Swagger version compatible with OAS 3.0
info:
 description: "This API allows you to track orders in ACME ecommerce"
 version: "1.0.0" # This is your api version
 title: "Tracking API"
host: "localhost:8080"
basePath: "/v1"
```

```bash
$ docker run -p 80:8080 -e SWAGGER_JSON=/mnt/tracking-api.yml -v /PATH/TO/FILE:/mnt swaggerapi/swagger-ui
```

![Swagger page containing metadata of the API](/assets/images/posts/2020-11-27-swagger-quick-start-guide/metadata.png)

The fun part of it starts now with you adding the paths key in your specification. It will hold specific information about your endpoints, like the parameters it will accept and the response it could return.

Going through the requirements, as an experienced engineer that you are, you soon realize that the four requirements from step 2 represent three endpoints: a) A POST to the API that will store the tracking number for a given account; b) A GET to return all tracking numbers that also accepts a parameter that will act as a filter, and c) A GET to return information about a specific tracking number. We will talk about authentication at the end.

With that in mind, we could start adding this information to our specification document. Just add a paths key that contains your endpoint’s path, their HTTP methods, and a summary.

```yaml
#...
paths:
 /tracking:
   post:
     summary: "Register a tracking number into a logged account"
   get:
     summary: "Returns all tracking numbers associated with a logged account.\
       \ It allows filtering tracking numbers by their status."
 /tracking/{trackingNumber}:
   get:
     summary: "Returns detailed information about a given tracking number."
```

![Swagger page with added endpoints](/assets/images/posts/2020-11-27-swagger-quick-start-guide/endpoints.png)

Now that you know what endpoints will be required in your API to fulfill the requirements, you can start detailing it with the parameters and expected responses.

Let's start with the GET request to /tracking. It accepts a nullable status parameter and returns a JSON with a list of tracking numbers. A tracking number is defined as a string.

```yaml
paths:
 /tracking:
   # ...
   get:
     summary: "Returns all tracking numbers associated with logged account.\
       \ It allows filtering tracking number by their status."
     produces:
     - "application/json"
     parameters:
     - name: "status"
       in: "query" # This is saying that this parameter must be given in a query string
       enum:
       - "created"
       - "processed"
       - "in_route"
       - "delivered"
       nullable: true
     responses:
       200:
         description: "Request completed. Returns an empty array if no tracking number was found."
         schema:
           type: "array"
           items:
             type: "string"
       502:
         description: "Server error. Try again in a few minutes."
```

![Swagger page with a specification for the tracking endpoint](/assets/images/posts/2020-11-27-swagger-quick-start-guide/get-tracking-spec.png)

With this specification, you have added a lot of useful information. The key produces tells what type of response is expected (a JSON, an XML, …), in parameters you documented what parameters are expected as a query string and what are the possible values of it. Also, you have now the possible HTTP status code that could be returned. Go on and add the same basic information to the other two endpoints.

```yaml
swagger: "2.0" # Swagger version compatible with OAS 3.0
info:
 description: "This API allows you to track orders in ACME ecommerce"
 version: "1.0.0" # This is your api version
 title: "Tracking API"
host: "localhost:8080"
basePath: "/v1"
paths:
 /tracking:
   post:
     summary: "Register one or more tracking numbers into a logged account"
     consumes:
     - "application/json"
     produces:
     - "application/json"
     parameters:
     - name: "body"
       in: "body"
       description: "Tracking numbers to be added into the logged account"
       required: true
       schema:
         type: "array"
         items:
           type: "string"
           examples: ["tracking_number_1", "tracking_number_2"]
     responses:
       201:
         description: "Request completed. The tracking numbers were successfully added into the account"
         schema:
           type: "array"
           items:
             type: "string"
             examples: ["tracking_number_1", "tracking_number_2"]
       502:
         description: "Server error. Try again in a few minutes."
   get:
     summary: "Returns all tracking numbers associated with logged account.\
       \ It allows filtering tracking number by their status."
     produces:
     - "application/json"
     parameters:
     - name: "status"
       in: "query" # This is saying that this parameter must be given in a query string
       enum:
       - "created"
       - "processed"
       - "in_route"
       - "delivered"
       nullable: true
     responses:
       200:
         description: "Request completed. Returns an empty array if no tracking number was found."
         schema:
           type: "array"
           items:
             type: "string"
       502:
         description: "Server error. Try again in a few minutes."
 /tracking/{trackingNumber}:
   get:
     summary: "Returns detailed information about a given tracking number."
     produces:
     - "application/json"
     parameters:
     - name: "trackingNumber"
       in: "path" # This is saying that this parameter must be given in the URL path
       description: "The tracking number to get detailed information"
       required: true
     responses:
       200:
         description: "Request completed. Returns details about the tracking number"
         schema:
           type: "object"
           properties:
             tracking_number:
               type: "string"
             status_log:
               type: "array"
               items:
                 type: "object"
                 properties:
                   status:
                     type: "string"
                   datetime:
                     type: "string"
       404:
         description: "Tracking number does not exist"
       502:
         description: "Server error. Try again in a few minutes."
```

Wow! Your specification looks neat! So many things were added at this point, let's talk about a few points.

Your `GET /tracking/{trackingNumber}` has a complex schema. As I have mentioned before, it follows JSON Schema rules to structure your object.

There is a key named "examples" that is not showing on the Example value tab. When you define it in the plural, it’ll be shown at the Model tab.

![Swagger's example tab which does not have the displaying the examples](/assets/images/posts/2020-11-27-swagger-quick-start-guide/example_tab.png)

![Swagger's model tab which does display the examples](/assets/images/posts/2020-11-27-swagger-quick-start-guide/model_tab.png)

There is one more thing missing in your specification. The authentication. To complete this task you just have to decide the authentication method (Basic authentication, apiKey, or Oauth2). Since the requirements ask only for an API key, to configure that is pretty simple. Add this configuration and use the security key to tell which endpoint will use it.

```yaml
paths:
 /tracking:
   post:
     # ...
     security:
     - api_key: []
   get:
     # ...
     security:
     - api_key: []
 /tracking/{trackingNumber}:
   get:
     # ...
     security:
     - api_key: []
securityDefinitions:
 api_key:
   type: "apiKey"
   name: "api_key"
   in: "header"
```

Now you have full documentation over your API. It tells the endpoints available, the authentication method, the expected input, and output, and it is testable. If you have your service running on the host given at the beginning, you will be able to perform some requests to it.