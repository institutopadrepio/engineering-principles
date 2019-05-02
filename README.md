# Engineering Principles
Our guidelines for building new applications and managing legacy systems.

## Backend development

For us doesn't not matter the language you choose to develop the application. It's importat to follow some rules when you decide to implement a different language that we are describing in other section of this document. 

The main objective of this section is to make sure we follow the rules below independent of the language we choose. We like Sandi Metzs Rules for OOP. These rules have helped us to keep our code simpler to understand and easier to change.

### *A class can be no longer than 100 lines of code.*

A class can be no longer than 100 lines of code. If a class has more than 100 lines of code, the possibility for this class to have more than one reason to change is pretty high

### *5 lines per method*

A method can be no longer than 5 lines of code.

* The first rule of functions is that they should be small. The second rule of functions is that they should be smaller than that. (Clean Code by Robert Martin) *

5 lines of code equals to 1 if statement

So this rule is asking us to

1. Do not write logic more complicated than an if statement
2. Write only 1 line per branch
3. Never use elsif

Every method we write can actually split into 4 basic steps and 2 optional steps

1. Collecting input
2. Performing work
3. Delivering output
4. Handling failures
5. (Optional) Diagnostics
6. (Optional) Cleanup

Base on this assumption, we can extract every step into a helper method and make every method tell a nice story. And every method will only contain ~5 lines of method calls.

## *4 parameters per method*

Pass no more than 4 parameters into a method.

Hash options are also parameters Do not fool ourselves with a hash full of parameters.

If there are more than 4 parameters, most of the time we can pull a new object out of some of the parameters.

## *1 object per view*

A view template should only refer to 1 object. 

Facade Pattern: A facade is an object that provides a simplified interface to a larger body of code. In Rails world, it's usually called Presenter.

```ruby
class ViewFacade

  def initialize(params)
    @object = params[:id]
    @params = params 
  end

  def object_attribute
    object.attribute
  end
  
  def associated_object_attribute
    object.associated_object.attribute
  end
  
  private 
  
  attr_accessor :params, :object
end
```

## We write tests for our code

Testing will provide a few main benefits when done correctly. First, it will help you develop the behaviors within your app. You may write a test that your model is validating the presence of an email. Then realizing you needed to include validations for the password or add a “retype password” input box.

Another great reason to test is to protect yourself against regressions within your application as your modifying code or adding new features. This will also give you confidence and assurance when your refactoring classes, collapsing code or removing extraneous code. If written tests pass both before and after changes you should be good to go! Another testing method is to write a test after you find an issue or bug in your application. Ensuring the fix works and does not plague your application in the future.

In our application we are using RSPEC 3 for unit tests and integration tests. We are following the pattern define in the better specs website. http://www.betterspecs.org/

## We like SOA (Service Oriented Architecture)

Rails follows a Model-View-Controller pattern. This raises questions around where programming logic should go once a Ruby on Rails application reaches a certain size. Generally, the principles are:

* Forget fat Models (don’t allow them to become bloated)
* Keep Views dumb (so don’t put complex logic there)
* And Controllers skinny (so don’t put too much there)

*So where should you put anything that’s more than a simple query or action?*

One way to support Object-Oriented Design is with the Service Object. This can be a class or module in Ruby that performs an action. It can help take out logic from other areas of the MVC files.


Our template for service looks like the code below:
 1. Strong initializer to receive parameters
 2. One public method to expose the service. The other methods should be private.
 3. Strong result from our service. Our services always return a Result objecti with the result of the operation, the error and the object
 4. Our service always handle expections
 
```ruby
class ServiceName < ApplicationService
  def initialize(params)
    @params = params 
  end
  
  def call
    Result.new(true, nil, object)
  rescue StandardError => e 
    Result.new(false, e.message, nil)
  end
  
  private 
  
  attr_accessor :params
end
```

## *Why do we need these rules?*

1. 100 lines per class: Simplified version of SLOC
2. 5 lines per method: Simplified version of SLOC and Cyclomatic Complexity
3. 4 parameters per method: Simplified version of ABC metric
4. 1 object per view: Simplified version of ABC metric

## Deployment pipeline

### Current Infrastructure

Our application is hosted at Heroku. Our deployment process is manual. So, you need to add in your git configuration two new remotes.

```bash
[remote "production"]
	url = https://git.heroku.com/padrepauloricardo.git
	fetch = +refs/heads/*:refs/remotes/production/*
```

```bash

[remote "staging"]
	url = https://git.heroku.com/stg-padrepauloricardo.git
	fetch = +refs/heads/*:refs/remotes/staging/*
```

We have travisCI for continuos integration. We didn't implement any hook for continuous delivery, so you need to run the commands below in order to deploy our application to staging server or production server.

``` git push production:master```
``` git push staging:master```


### Our middle term vision

1. We want to start working with continuous delivering for our application. We are improving the test coverage in order to stat that. 
2. Using Kubernetes. Our application already runs using Docker, so our next step would be configure our rails application to run using Kubernetes
3. Choose another host. We are considering three new possibilities: Amazon AWS, Google Cloud or Digital Ocean.

## API

### API First and API as a product

We prefer to adopt "API First" and "API as a Product" as key engineering principles. In a nutshell, API First encompasses a set of quality-related standards (including the API guidelines and tooling) and fosters a peer review culture; it requires two aspects:

* define APIs outside the code first using a standard specification language (Open API 2.0)
* get early review feedback from peers and client developers

### RESTful APIs with JSON payload

We prefer REST-based APIs with JSON payloads to SOAP. Distributed SOAs following the REST style have a looser coupling between client and server implementations and comes with less rigid client/server contracts that do not break if either side make certain changes. Hence it is easier to build interoperating distributed systems that can be evolved in parallel by different teams while continuing to work. REST-like APIs with JSON payload is the most widely accepted and used service interfacing style in the internet web service industry.

Be conservative in what you send, be liberal in what you accept. APIs must be evolved without breaking any consumers.

### General Guidelines for API

#### Use nouns but no verbs

For an easy understanding use this structure for every resource:

Do not use verbs:

* /getAllCars
* /createNewCar
* /deleteAllRedCars

#### GET method and query parameters should not alter the state

Use PUT, POST and DELETE methods  instead of the GET method to alter the state.
Do not use GET for state changes:

* GET /users/711?activate or
* GET /users/711/activate

#### Use plural nouns

Do not mix up singular and plural nouns. Keep it simple and use only plural nouns for all resources.

* /cars instead of /car
* /users instead of /user
* /products instead of /product
* /settings instead of /setting

#### Use sub-resources for relations

If a resource is related to another resource use subresources.

* GET /cars/711/drivers/ Returns a list of drivers for car 711
* GET /cars/711/drivers/4 Returns driver #4 for car 711

#### Use HTTP headers for serialization formats

Both, client and server, need to know which format is used for the communication. The format has to be specified in the HTTP-Header.

Content-Type defines the request format.
Accept defines a list of acceptable response formats.

#### Provide filtering, sorting, field selection and paging for collections

*Filtering*

Use a unique query parameter for all fields or a query language for filtering.

*Sorting*

Allow ascending and descending sorting over multiple fields.

*Paging*

Use limit and offset. It is flexible for the user and common in leading databases. The default should be limit=20 and offset=0

* GET /cars?offset=10&limit=5

To send the total entries back to the user use the custom HTTP header: X-Total-Count.

Links to the next or previous page should be provided in the HTTP header link as well. It is important to follow this link header values instead of constructing your own URLs.

#### Version your API

Make the API Version mandatory and do not release an unversioned API. Use a simple ordinal number and avoid dot notation such as 2.5.

We are using the url for the API versioning starting with the letter „v“

#### Handle Errors with HTTP status codes

It is hard to work with an API that ignores error handling. Pure returning of a HTTP 500 with a stacktrace is not very helpful.

#### Use error payloads

All exceptions should be mapped in an error payload. Here is an example how a JSON payload should look like.

```json
{
  "errors": [
   {
    "userMessage": "Sorry, the requested resource does not exist",
    "internalMessage": "No car found in the database",
    "code": 34,
    "more info": "http://dev.mwaysolutions.com/blog/api/v1/errors/12345"
   }
  ]
} 
```

#### Allow overriding HTTP method

Some proxies support only POST and GET methods. To support a RESTful API with these limitations, the API needs a way to override the HTTP method.

Use the custom HTTP Header X-HTTP-Method-Override to overrider the POST Method.




## References
