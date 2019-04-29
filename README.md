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

## We write tests for our code

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

## Deployment

## API

## Services

