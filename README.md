# Laravel Basics Documentation
This document summarizes my journey in learning the basics of Laravel, focusing on key areas such as CRUD operations, authentication and authorization, Jetstream integration, and using Laravel Backpack.

## Table of Contents

- [Introduction](#introduction)
- [CRUD Operations](#crud-operations)
- [Authentication and Authorization](#authentication-and-authorization)
- [Jetstream Authentication](#jetstream-authentication)
- [Backpack Integration](#backpack-integration)
- [Conclusion](#conclusion)

## Introduction

Here are the pre-requisites of creating a laravel project and installation process : 
- [Requirements](#requirements)
- [Installation](#installation)
- [Project Structure](#project-structure)
- [Dependencies](#dependencies)
- [Usage](#usage)
- [Conclusion](#conclusion)


This project is a simple PHP application that utilizes Composer to manage dependencies and autoloading. Composer makes it easier to integrate third-party libraries and maintain the project.

## Requirements

Before starting, ensure you have the following installed on your system:

- **PHP** (version 10.x)
- **Composer** (latest version)

## Installation

Follow these steps to set up the project:

1. **Create your project (using composer)**

   ```bash
   composer create-project laravel/laravel example-app
   cd example-app
   php artisan serve


## Database and Migration

Follow these steps to set up the project:

1. **Config your database (for postgreSQL)**
   Go to .env file from your project file and config as below
   ```bash
    DB_CONNECTION=pgsql
    DB_HOST=127.0.0.1
    DB_PORT=5432
    DB_DATABASE=your-db-name
    DB_USERNAME=your-server-username
    DB_PASSWORD=your-server-password

    ```

    ```bash
    php artisan migrate
    ```

## Project Structure

The project is organized in a way that follows common best practices for PHP applications. Below is an overview of the directory and file structure:





## CRUD Operations

In this section, I learned how to perform Create, Read, Update, and Delete (CRUD) operations in Laravel. This involved:

1. **Creating Controllers and Models**: Setting up controllers and models to handle data manipulation.
2. **Routing**: Defining routes for accessing CRUD functionalities.
3. **Views**: Developing views with Blade templates to display, input, and edit data.
4. **Database Migrations**: Managing database schema changes using Laravel migrations.
5. **Eloquent ORM**: Interacting with the database using Eloquent ORM for efficient data handling.

### Example Code:

```php
// Route example for CRUD operations
Route::resource('items', ItemController::class);
public function update(User $user, Post $post)
{
    return $user->id === $post->user_id;
}
```


