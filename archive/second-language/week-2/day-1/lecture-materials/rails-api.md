---
track: "Second Language"
title: "Rails API"
week: 2
day: 1
type: "lecture"
---


# Rails API


### Lesson Objectives
_After this lesson, students will be able to:_

- Set up a basic Rails API server using scaffold
- Set up another server to consume that API using React
- Configure CORS to allow Cross Origin Resource Sharing

<hr>

## Why make a Rails API?

Today, we will add a front-end to our applications.

What we are doing with Rails is making an API, and this is the **back end**.

[API on Wikipedia](https://en.wikipedia.org/wiki/Application_programming_interface)

![](https://i.imgur.com/zm4EeFX.png)

We are providing a _service_ that either ourselves or others will be able to access. Our service happens to be **data, in JSON format**.

Rails APIs are used as backends for mobile apps as well as desktop apps.

With our API, the data is its own thing. There is a complete _separation of concerns_ between the data and the display because they are running on separate servers. You swap out any old display and you do not need to change a single thing on the backend.

## Rails server is API only

Instead of having a public folder or any frontend stuff associated with our Rails server, we will leave our Rails server untouched by any frontend code. It will _just_ serve JSON.

We will then create another server to **consume the API**, as if we were some other developer or team whose job is just to make the front-end.

**What we will be doing**

* We will review the steps for creating a Rails API for our backend, and serve an **index** endpoint

* We will create a new server _separate_ from our Rails server for our frontend -- it will only serve static files.

* We will use our frontend server to consume the API coming from the backend server. Our frontend server will serve React. The easiest way for us to serve React is with Express, plus, we will have a stack that is completely JavaScript.


<br>
<hr>


# TWO SERVERS

**Let's start a project that will use two apps**

Server One: provides the API

Server Two: consumes the API


## Setup

* `mkdir noticeboard_app`
* `cd noticeboard_app`

In `student_examples` for today, make a directory `noticeboard_app` to contain both our Rails server and our Express server (We will be using `Create React App`). Do not put the `_api` there! We will do that for our Rails server later. This is just a directory.

Inside this directory we will put our two servers. We are only putting them together in here for convenience so that we can open them in our text editor easily. They can exist on separate systems (that is the point).


## Make Directories

Inside `noticeboard_app` make the rails server:

Remember to add flags for

* api option `--api`
* database option `-d postgresql`
* skip git option (Rails 5.1 and above) `--skip-git`
* skip actve storage option (Rails 5.2 and above) `--skip-active-storage`

* `rails new noticeboard_app_api --api -d postgresql --skip-git --skip-active-storage`

![](https://i.imgur.com/o2j1o7Y.png)

Do not go into the directory yet.

Make another directory on the same level as the rails directory called `noticeboard_app_frontend`. This will **not** be a Rails project (we will make an Express app later). Just make the directory.

* `mkdir noticeboard_app_frontend`

Tree structure: The `noticeboard_app` directory contains both the `api` and `frontend` directories.


![](https://i.imgur.com/3rGxbEe.png)

* Open up the entire `noticeboard_app` directory in your text editor. You should see both `noticeboard_app_api` and `noticeboard_app_frontend` in there.


# &#x1F6E0; SCAFFOLD THE RAILS API

Let's see how fast we can make our Rails API. (Aim: 15 minutes with explanations)

This morning we will use the `generate scaffold` command to generate boilerplate code for a given resource. Our app will be a noticeboard, and the resource will be notices.

* First, go into the Rails directory on the command line.

* Then, create the database. `rails db:create`

* Scaffold the resource:

```bash
rails g scaffold notice title author content
```

> `rails g scaffold model_name column column column`

This will generate all the folder, files, and code needed for a model called `Notice` that has columns for `title`, `author`, and `content` all of datatype string. String is the default datatype.

![](https://i.imgur.com/Ctfxp4F.png)

## What did `generate scaffold do`?

**Created files for migration, model, controller, and routes**

* `db/migrate`. Scaffold has created a migration file/folder for our Notice resource. There is a boilerplate method for creating the **notices** table. Title, author, and content columns (strings) are ready to go.

* `app/models/notice.rb`. Scaffold has created a file our Notice resource. The model has been set up for us.

* Look in `config/routes`. Scaffold has set our resources for us. The `rails routes` command will tell us what controllers and actions we should use:

```bash
 Prefix Verb   URI Pattern            Controller#Action
notices GET    /notices(.:format)     notices#index
        POST   /notices(.:format)     notices#create
 notice GET    /notices/:id(.:format) notices#show
        PATCH  /notices/:id(.:format) notices#update
        PUT    /notices/:id(.:format) notices#update
        DELETE /notices/:id(.:format) notices#destroy
```

* `notices_controller.rb`. Scaffold has provided all of the relevant routes for our CRUD actions.
**index**, **show**, **create**, **update**, and **delete**.

* Scaffold has placed **instance variables** such as `@notices = Notice.all` for scoping. Helper methods such as `set_notice` can operate on the more globally available `@notice` variable.

* There is a new helper method `set_notice` invoked with a `before_action` method. All it does is find a notice according to its id before specific routes are hit. It is configured to work **only:** for the show, update, and destroy methods.

* The controller is functionally exactly the same as what we have seen before.

* In the create method, **location** is an option for redirecting the page by setting the **Location** option in the response object. We probably just want the JSON and don't want our server to try to perform a redirect on the client, which can cause errors in Postman, etc. If you run into errors, remove `location: @notice` from the **create** action.



## MIGRATE

Scaffolding does not interact with the database (for good reason).

Remember, all these Ruby migration commands are methods. If we add parens, they will still work the same.

```ruby
  def change
    create_table(:notices) do |t|
      t.string(:title)
      t.string(:author)
      t.string(:content)

      t.timestamps
    end
  end
```

* Run the migration: `rails db:migrate`



## SEED

Inside the Gemfile include Faker

* `gem 'faker'`

![](https://i.imgur.com/7wpsKcb.png)

Install the gem with `bundle` (shorthand for `bundle install`. `install` is the default option for the `bundle` command).

In `seeds.rb` use Faker to fill out the fields:

```ruby
100.times do
    Notice.create(
      title: Faker::Hipster.sentence(word_count: 3),
      author: Faker::Team.name,
      content: Faker::Lorem.paragraph
    )
  end

puts "Seeded database"
```

* Seed with `rails db:seed`

* Run the server with `rails s`

* Check out your API at `localhost:3000/notices`


**DONE with our Scaffold!**
