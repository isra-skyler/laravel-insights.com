# Laravel 9 CRUD Operations with Eloquent ORM: A Step-by-Step Guide
Building web applications often requires creating, reading, updating, and deleting data from a database - collectively known as CRUD operations. Mastering these CRUD functions in Laravel 8 is essential for any developer.
In this comprehensive tutorial, I will walk through CRUD operations from start to finish in Laravel 9 using Eloquent ORM. We will cover database migration, creating Eloquent models, building CRUD routes and controllers, input validation, and integrating with the front-end UI.
Laravel's Eloquent ORM provides a beautiful, simple ActiveRecord implementation for working with your database. Eloquent makes it easy to retrieve, insert, and delete records from associated database tables. We will leverage Eloquent models instead of writing raw SQL queries.

## Table Of Content
- [Understanding CRUD in Laravel 9](#understanding-crud-in-laravel-9)
- [Setting Up Your Laravel 9 Project](#setting-up-your-laravel-9-project)
- [Database Configuration and Migration](#database-configuration-and-migration)
- [Creating Models with Eloquent](#creating-models-with-eloquent)
- [Building the CRUD Functions](#building-the-crud-functions)
- [Implementing Validation](#implementing-validation)
- [User Interface (UI) Integration](#user-interface-ui-integration)
- [Testing Your Laravel 9 CRUD Application](#testing-your-laravel-9-crud-application)
- [Deploying Your Laravel 9 CRUD App](#deploying-your-laravel-9-crud-app)
- [Conclusion](#conclusion)
## Understanding CRUD in Laravel 9
CRUD stands for Create, Read, Update, and Delete - the four basic functions involved in persistent data storage. Performing CRUD operations is essential for any dynamic web application.
Laravel 9 provides an elegant way to implement CRUD functionality using:
* **Database Migrations** - For creating/modifying database schemas
* **Eloquent ORM** - For interacting with database tables through models
* **Controllers** - For handling request/response logic
* **Blade Templates** - For rendering CRUD views
### Why CRUD Matters
Being able to efficiently perform CRUD operations is critical for building functional web apps. For example:
* Creating new records (Create)
* Displaying records (Read)
* Editing existing records (Update)
* Deleting records (Delete)
Without CRUD abilities, applications are static and lack persistence.
### Introducing Eloquent ORM
Laravel's Eloquent ORM (Object-Relational Mapper) makes it simple to implement CRUD operations without writing excessive SQL queries.
Eloquent allows you to query the database using PHP objects instead of SQL syntax. The table rows become instances of a corresponding Eloquent "Model" class.
For example, we can have a Post model that maps to a posts database table. Each Post instance represents a row in that table.
We will dive deeper into creating Eloquent models later in this tutorial.

## Setting Up Your Laravel 9 Project
Let's go through the steps to create a new Laravel 9 project and set up our development environment.
### Install Laravel 9
First, make sure you have Composer installed on your machine.
Install a new Laravel 9 project using Composer:
```
composer create-project laravel/laravel `example-app`
```
The above will create a `example-app` directory containing a fresh Laravel 9 installation.
### Set Up Database
Next, create a new database for your application using a tool like MySQL.
Update your Laravel `.env` file with the appropriate database connection credentials:

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel_crud
DB_USERNAME=root
DB_PASSWORD=
```
Make sure to enter your database name, host, port, username and password.
### Install Dependencies
Navigate into the project directory and install dependencies:
```
cd `example-app`
composer install
```
This will pull in Laravel's core packages and dependencies.
Your Laravel 9 environment should now be ready! We can now start building out the CRUD functionality.

## Database Configuration and Migration
Now that our Laravel 9 project is set up, let's configure the database and run migrations.
### Configure Environment
Your Laravel database credentials should already be updated in the `.env` file.
Laravel will read this file to connect with the database. Make sure it is configured properly.
### Create Migration
Laravel includes a schema builder and easy database migration system. We can use Artisan commands to generate migrations:
```
php artisan make:migration create_posts_table
```
This creates a new migration file in `database/migrations`. Next, define the schema in the up() method:
```php
public function up()
{
  Schema::create('posts', function (Blueprint $table) {
    $table->id();
    $table->string('title');
    $table->text('body');
    $table->timestamps();
  });
}
```
We now have a migration that will create a posts table with columns for ID, title, body and timestamp fields.
### Run Migrations
Finally, execute the migrations to update the database schema:
```
php artisan migrate
```
This will create all tables defined in your migration files. Our posts table is now created and ready for CRUD operations!

## Creating Models with Eloquent
Now that our database is set up, let's create an Eloquent model to interact with the posts table.
Eloquent models allow us to query the database using PHP objects and relationships.
### Generate Model
Use the Artisan command to generate a Post model:
```
php artisan make:model Post
```
This will create a Post model in `app/Models/Post.php`.
### Define Properties
Next, we need to define the model properties. Update the model to match the database columns:
```php
class Post extends Model
{
  protected $fillable = ['title', 'body'];
}
```
The $fillable property allows mass assignment of those fields.
### Relationships
You can define relationships between models:
```php
public function comments()
{
  return $this->hasMany(Comment::class); 
}
```
This sets up a one-to-many relationship between the Post and Comment models.
Our Post model is now ready to for CRUD operations! Next we will build out the controllers.

## Building the CRUD Functions
Now we can build out the CRUD routes and controller methods to perform create, read, update, and delete operations with Eloquent.
### Define Routes
First, include RESTful resource routes in `routes/web.php`:
```php
Route::resource('posts', PostController::class);
```
This defines routes for all CRUD actions, like store, update, destroy, etc.
### Create Controller
Next, generate a PostController with Artisan:
```
php artisan make:controller PostController --resource
```
The --resource flag creates the required REST methods.
### Implement CRUD Methods
Now we can fill in the controller methods. For example:
```php
public function store(Request $request) 
{
  $post = Post::create($request->all());
  return redirect()->route('posts.index');
}
```
The `store()` method handles creating new posts. Other methods will handle updating, deleting, etc.
We can make queries using Eloquent to CRUD data from the database.
### Eager Load Relationships
Use `with()` to eager load related models:
```php
$posts = Post::with('comments')->get();
```
This will retrieve all posts and their comments in a single query.

## Implementing Validation
When building a CRUD app, it's important to validate data before inserting or updating in the database. Laravel makes validation easy.
### Validation Rules
Define validation rules in the controller methods:
```php
public function store(Request $request)
{
  $rules = [
    'title' => 'required|max:255',
    'body' => 'required'
  ];
  
  $this->validate($request, $rules);
}
```
This checks the title and body fields on new posts.
### Form Request Validation
For more complex validation, you can create a Form Request class:
```
php artisan make:request StorePostRequest
```
Then define the rules in that class instead of the controller.
### Displaying Errors
Laravel can automatically flash errors to the session on validation failure:
```php
$request->validate();

return redirect()
  ->back()
  ->withErrors($validator);
```
The errors will be available in the view to display.
### Custom Error Messages
You can customize the error messages using custom validator language files.
This allows complete control over validation feedback shown to the user.
Proper validation helps catch issues early and provide user-friendly feedback.

## User Interface (UI) Integration
Now that we have our backend CRUD functionality implemented, let's integrate it into the front-end UI.
### Blade Templates
Laravel uses Blade templates to render views and layouts.
For example, we can display our list of posts in `resources/views/posts/index.blade.php`:
``` php
@extends('layouts.app')

@section('content')
  @foreach ($posts as $post)
    <h2>{{ $post->title }}</h2>
    <p>{{ $post->body }}</p>   
  @endforeach
@endsection
```
The @extends directive pulls in the main app layout, while @section denotes content for that section.
### Pass Data to Views
In the controller, pass data to views:
```php
public function index()
{
  $posts = Post::all();

  return view('posts.index', ['posts' => $posts]);
}
```
The `$posts` variable will be available in the view.
### Forms
Build forms to submit data to routes. For example:
```html
<form action="/posts" method="POST">
  @csrf
  <input name="title">
  <textarea name="body"></textarea>
  
  <button type="submit">Submit</button>
</form>
````
This will `POST` the form data to the `/posts` route.
### Navigation
Use anchor tags to build navigation between views:
```html
<a href="/posts/create">New Post</a>
```
This provides a link to the form to create new posts.
The frontend UI allows users to interact with the CRUD functionality we built.

## Testing Your Laravel 9 CRUD Application
Testing is important to ensure the CRUD functionality works properly. Laravel makes testing easy.
### Write Tests
Laravel includes integration testing tools for mocking requests and assertions.
**Some examples:**
```php
public function test_store_post()
{
  $response = $this->post('/posts', [
    'title' => 'Test Post',
    'body' => 'Test body text'
  ]);

  $response->assertRedirect('/posts');
}

public function test_post_title_required() 
{
  $response = $this->post('/posts', [
    'body' => 'Test body text'
  ]);

  $response->assertSessionHasErrors('title');
}
```
The first test checks that a new post can be created.
The second checks the validation rules are working properly.
### Execute Tests
Run your test suite:
```
./vendor/bin/phpunit
```
This will execute all tests and provide a report.
### Test Database
Use a separate test database so tests don't interact with real data:
```
DB_CONNECTION=mysql_test
```
Migrate it to run tests:
```
php artisan migrate --database=mysql_test
```
Testing ensures everything continues working as expected as you build out features.

## Deploying Your Laravel 9 CRUD App
Once your application is complete and tested, it's time to deploy it to a production server for the world to use.
## Production Environment
Make sure the production environment is properly configured:
Set the `APP_ENV` variable to `production`
Update configuration for production database, cache, queues, etc.
### Optimization
Optimize the app for faster performance:
* Run `php artisan config:cache` to cache config
* Run `php artisan route:cache to` cache routes
* Run `php artisan view:cache to` cache views
* Use a cache driver like Redis for faster cache
### Web Server
Use a web server like Nginx or Apache to serve the Laravel app:
* Configure the document root to Laravel's `public` directory
* Point your domain's DNS to the server IP address
* Allow access through firewalls
### Security
Secure the application from threats:
* Require HTTPS connections
* Enable CORS if using API
* Rotate encryption keys
* Restrict access with firewall rules
With proper configuration, your Laravel CRUD app can be robust and production-ready.

## Conclusion
Congratulations, you have now built a full-featured Laravel 9 application with CRUD functionality using Eloquent ORM!
Mastering these fundamentals will give you a solid foundation for building robust, data-driven web applications in Laravel.
There are many additional features you can explore like authentication, APIs, advanced relationships, and more.
If you're looking to take your Laravel project to the next level and you happen to be in the beautiful state of Washington, we've got exciting news for you. Our team at [Hybrid Web Agency](https://hybridwebagency.com/), a leading Laravel development company, specializes in providing Professional Custom Laravel development services across various cities in Washington state.
Our Expertise Spans Across Washington Cities Like:
* [Marysville](https://hybridwebagency.com/marysville-wa/custom-laravel-development-services/)
* [Bothell](https://hybridwebagency.com/bothell-wa/custom-laravel-development-services/)
* [Lynnwood](https://hybridwebagency.com/lynnwood-wa/custom-laravel-development-services/)
* [Renton](https://hybridwebagency.com/renton-wa/custom-laravel-development-services/)
* [Vancouver](https://hybridwebagency.com/vancouver-wa/custom-laravel-development-services/)

I hope you found this tutorial helpful! Let me know if you have any other questions. Happy coding!
