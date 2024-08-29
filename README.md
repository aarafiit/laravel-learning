# Laravel Basics Documentation
This document summarizes my journey in learning the basics of Laravel, focusing on key areas such as CRUD operations, authentication and authorization, Jetstream integration, and using Laravel Backpack.

## Table of Contents

- [Introduction](#introduction)
- [CRUD Operations](#1-crud-operations)
- [Customized Authorization and Authentication](#2-customized-authorization-and-authentication)
- [Jetstream Authentication](#jetstream-authentication)
- [Backpack Integration](#backpack-integration)
- [Conclusion](#conclusion)

## Introduction

Here are the pre-requisites of creating a laravel project and installation process:
- [Requirements](#requirements)
- [Installation](#installation)
- [Project Structure](#project-structure)


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
   ```
   
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


### 1. CRUD Operations
- Implemented Create, Read, Update, and Delete (CRUD) functionalities for managing resources in the application.
- Structured database migrations and models to efficiently handle data operations.
- Developed user-friendly interfaces for managing records.
##### Example Code
   ```bash
   // Create
  public function store(Request $request)
  {
     $request->validate([
          'name' => 'required|string|max:255',
          'price' => 'required|numeric',
          'category' => 'required|string|max:255',
      ]);

      Product::create($request->all());

      return redirect()->route('products.index')
                     ->with('success', 'Product created successfully.');
  }
  // Read
  public function index()
{
    $products = Product::all();
    return view('products.index', compact('products'));
}
// Update
public function update(Request $request, Product $product)
{
    $request->validate([
        'name' => 'required|string|max:255',
        'price' => 'required|numeric',
        'category' => 'required|string|max:255',
    ]);

    $product->update($request->all());

    return redirect()->route('products.index')
                     ->with('success', 'Product updated successfully.');
}
// Delete
public function destroy(Product $product)
{
    $product->delete();

    return redirect()->route('products.index')
                     ->with('success', 'Product deleted successfully.');
}

   ```

### 2. Customized Authorization and Authentication
- Integrated Laravel Breeze for authentication and user management also use custom authorization.
- Customized the authorization process to support different user roles (Admin, User).
- Restricted access to specific routes and features based on user roles using middleware.
##### Example Code
   ```bash
   // Authentication
   // Login
   public function login(Request $request)
       {
           $credentials = $request->only('email', 'password');
   
           if (Auth::attempt($credentials, $request->filled('remember'))) {
               return redirect(route('product.index')); // Redirect to the intended page or home
           }
   
           return back()->withErrors([
               'email' => 'The provided credentials do not match our records.',
           ])->withInput();
       }
   // Registration
       public function register(Request $request)
       {
           $this->validate($request, [
               'name' => 'required|string|max:255',
               'email' => 'required|string|email|max:255|unique:users',
               'password' => 'required|string|min:6|confirmed',
           ]);
   
           User::create([
               'name' => $request->name,
               'email' => $request->email,
               'password' => Hash::make($request->password),
           ]);
           return redirect()->route('user.login')->with('success', 'Registration successful. Please login.');
       }
   ```
### 3. Dynamic Search and Filtering
- Implemented dynamic search functionality allowing users to search products by name.
- Developed filters for products based on price, category, and other relevant criteria.
- Ensured seamless integration of search and filter operations for an improved user experience.
##### Example Code
```bash
// Search and filter Product
public function index(Request $request)
    {
        // Get all categories for the filter dropdown
        $categories = Category::pluck('name');

        // Build the query
        $query = Product::query();

        // Apply search filter
        if ($search = $request->input('search')) {
            $query->whereRaw('LOWER(name) LIKE ?', ['%' . strtolower($search) . '%']);
        }

        // Apply category filter
        if ($category = $request->input('category')) {
            if ($category != '') {
                $query->where('category', $category);
            }
        }

        // Get filtered products
        $products = $query->get();

        if ($request->ajax()) {
            return view('products.partials.product-list', compact('products'))->render();
        }

        return view('products.index', compact('products', 'categories'));
    }
// Srcript for dynamic search
      <script>
          document.getElementById('search').addEventListener('input', function() {
            const searchTerm = this.value.toLowerCase();

          // Fetch the filtered products dynamically
            fetch('{{ route('product.index') }}?search=' + searchTerm)
          .then(response => response.text())
             .then(html => {
                const parser = new DOMParser();
                const doc = parser.parseFromString(html, 'text/html');
                const newProductContainer = doc.getElementById('product-container');
                document.getElementById('product-container').innerHTML = newProductContainer.innerHTML;
             });
            });
      </script>
```
### 4. Middleware for Route Protection
- Created custom middleware to protect routes and restrict access based on user roles.
- Ensured that only admins have access to critical operations such as adding, updating, or deleting products.
- Implemented role-based access control (RBAC) to enhance application security.
##### Example Code
```bash
Route::middleware(['role:admin'])->group(function (){
    Route::get('/product/create',[ProductController::class,'create'])->name('product.create');
    Route::get('/product/{product}/update',[ProductController::class,'edit'])->name('product.update');
});
```
