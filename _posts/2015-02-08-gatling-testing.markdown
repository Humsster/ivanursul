---
layout: "post"
title:  "Testing RESTful CRUD Web Service with Gatling Tool"
date: 2015-02-08 09:30:45
permalink: gatling-testing
---


If you follow the right way of designing your web app, and choosed RESTful architecture, then it should be easy for you to test your appplication. By the REST style, you should implement CRUD operations for each entity in your application. What does it mean:

You should be able to Create,Read, Update, Delete(CRUD) your entities.
You should provide statuses for each operations. For example, for Create operation you should have 201 status in case of succesful operation.
You application must have good exception handling. For example, if you will make GET request for retrieving some entity called person with id - 1, and there will be no such record in your storage with this id, then you need to tell your user, that there is no such person, and set status to 404(Not found)
So, given that you will keep in mind all information above and will design your app with such instructions.

Now it's time to write some tests. Which framework you should choose?

I choosed Gatling framework. Reasons:

* It runs on jvm
* It has maven module

#### Configuration

As I mentioned above, I will use maven for building my app.

So, I will create a separate module for running my integration tests.

![](assets/images/Screen-Shot-2015-01-03-at-4-33-55-PM.png)

Gatling integration module

with jar packaging

Setup you pom.xml like in below:

<script src="https://gist.github.com/johnyUA/c96c985e54e5168bc3ea.js"></script>

#### Scala IDE

To write tests with gatling, you need to have IDE with scala support.I choosed Scala ready IDE. You can download it here. After scala installation, Right click on project -> Configure - > Add Scala Nature.

Then create Source Folder in src/test/scala.

Now you are ready to write tests.

#### Creating first test

To have scalable structure, create a Scala class - org.lnu.is.integration.IntegrationTest.

<script src="https://gist.github.com/johnyUA/d1c2ab901d5f827980a0.js"></script>

This class will be entry point for all future tests.

Now let's create integration test for person(just an example), note that it should be object, not a class - it's a special singleton construction in scala.

<script src="https://gist.github.com/johnyUA/32c4d07f9170ee8aae12.js"></script>

Edit your **IntegrationTest** class

<script src="https://gist.github.com/johnyUA/f9754f16384df60088ad.js"></script>

#### Adding resources

Note, that all data(json templates, etc..) are store in data folder

data/person/post.json:

<script src="https://gist.github.com/johnyUA/d8f7f01f4317b4c3ca22.js"></script>

data/person/put.json:

<script src="https://gist.github.com/johnyUA/dc8d00afb9bed69ef34c.js"></script>

#### Describing scenario

The idea of scenario is:

* Call POST method to create new person, get generated identifier.
* Call GET method /persons/{identifier} and make sure that there is such record in the system and response has status 200(OK).
* Call PUT method to update some columns.
* Call GET method to make sure, that columns from above method are updated.
* Call DELETE method
* Call GET method and make sure, that status is 404(Not Found)

Generating custom values

There are cases, where you need to generate unique value for each request.

For example, some person identifier needs to be unique.

For solving such issue I use session configuration:

    .exec(session => {
      session
        .set("idnum", UUID.randomUUID())})
and in json file I just make a placeholder:

> "identifier": "${idnum}",

That seems to be all, that you need .

#### Running test

Start your server, open terminal, go to your integration module folder and execute

> mvn clean install

Watch the results.