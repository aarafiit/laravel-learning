# Laravel CRUD and Authorization Project

## Overview

This project is a demonstration of my skills in Laravel, focusing on CRUD operations, authorization using Laravel Breeze, dynamic search and filtering, and route protection based on user roles. The project showcases a robust user management system with admin and user roles, ensuring secure access and functionality throughout the application.

## Features

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
``` base
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
``` base
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

        // Apply price range filter
        $priceMin = $request->input('price_min');
        $priceMax = $request->input('price_max');
        if ($priceMin || $priceMax) {
            if ($priceMin) {
                $query->where('price', '>=', $priceMin);
            }
            if ($priceMax) {
                $query->where('price', '<=', $priceMax);
            }
        }

        // Apply quantity range filter
        $qtyMin = $request->input('qty_min');
        $qtyMax = $request->input('qty_max');
        if ($qtyMin || $qtyMax) {
            if ($qtyMin) {
                $query->where('qty', '>=', $qtyMin);
            }
            if ($qtyMax) {
                $query->where('qty', '<=', $qtyMax);
            }
        }
        if ($sort = $request->input('sort')) {
            $query->orderBy('name', $sort);
        } else {
            // Default sorting by ascending order
            $query->orderBy('name', 'asc');
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
``` base
Route::middleware(['role:admin'])->group(function (){
    Route::get('/product/create',[ProductController::class,'create'])->name('product.create');
    Route::get('/product/{product}/update',[ProductController::class,'edit'])->name('product.update');
});
```
## Setup Instructions

### Prerequisites
- PHP 8.x
- Composer
- Laravel 10.x
- MySQL or PostgreSQL

### Installation
1. Clone the repository:
   ```bash
   cd yourproject
   git https://github.com/Rana-Hossain/laravel-crud-application.git
   ```
