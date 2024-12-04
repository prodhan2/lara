composer install

php artisan key:generate

composer create-project laravel/laravel book-store
cd book-store

php artisan make:model Book -mcr

php artisan migrate

php artisan db:seed

# DatabaseSeeder.php

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use App\Models\Sujan;
use Faker\Factory as Faker;

class DatabaseSeeder extends Seeder
{
    /**
     * Seed the application's database.
     */
    public function run()
    {
        // Truncate the sujan table before seeding new data
        Sujan::truncate();

        $faker = Faker::create();

        // Loop to create 50 fake entries
        foreach (range(1, 50) as $index) {
            Sujan::create([
                'name' => $faker->name, // Full name
                'father' => $faker->name('male'), // Father's name
                'age' => $faker->numberBetween(18, 70), // Age between 18 and 70
                'phone' => $faker->phoneNumber, // Phone number
                'email' => $faker->unique()->safeEmail, // Unique email
                'address' => $faker->address, // Address
                'dob' => $faker->date('Y-m-d', '2005-12-31'), // Date of birth
                'gender' => $faker->randomElement(['Male', 'Female', 'Other']), // Gender
                'job' => $faker->jobTitle, // Job title
                'salary' => $faker->numberBetween(15000, 100000), // Salary between 15,000 and 100,000
                'place' => $faker->city, // City or place
                'nid' => $faker->unique()->numerify('############'), // 12-digit National ID
                'marital_status' => $faker->randomElement(['Single', 'Married', 'Divorced', 'Widowed']), // Marital status
                'company' => $faker->company, // Employer company
                'joining_date' => $faker->date('Y-m-d', 'now'), // Date of joining
            ]);
        }
    }
}

```

# Controller

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
Use App\Models\Book;

class BookController extends Controller
{

    public function index(Request $request)
    {
        // Capture the search query
        $search = $request->input('search');

        // Fetch books with search and paginate the results
        $books = Book::where('title', 'LIKE', '%' . $search . '%') // Replace 'title' with the appropriate column
            ->orWhere('author', 'LIKE', '%' . $search . '%') // Optional: add other columns to search in
            ->paginate(30); // Adjust the number of items per page

        return view('books.index', compact('books', 'search'));
    }

    public function show( $id)
    {
         $books = Book::find($id);
         return view('books.show', compact('books'));
        // echo "$id";
    }

    public   function create()
    {
        return view ('books.create');
    }

    public function store(Request $request)
{
    // Validate the input fields, including description
    $request->validate([
        'id' => 'required|integer|unique:books,id',
        'title' => 'required|string|max:255',
        'author' => 'required|string|max:255',
        'description' => 'required|string|max:500', // Add validation for description
    ]);

    // Create a new Book instance and save it
    $book = new Book();
    $book->id = $request->id;
    $book->title = $request->title;
    $book->author = $request->author;
    $book->description = $request->description; // Save the description
    $book->save();

    // Redirect to the 'show' route with the book's ID
    // return redirect()->route('book.show', ['id' => $book->id]);
    return redirect()->route('index');
}

public   function  edit( $id)
{
    $book = Book::findOrFail($id);
    //  dd($book->toArray());
    return view('books.edit', compact('book'));

    // return view ('books.edit');
}

public function update(Request $request, $id)
{
    // Validate the input data
    $request->validate([
        'title' => 'required|string|max:255',
        'author' => 'required|string|max:255',
        'description' => 'required|string|max:500',
    ]);

    // Find the book and update its details
    $book = Book::findOrFail($id);
    $book->title = $request->title;
    $book->author = $request->author;
    $book->description = $request->description;
    $book->save();

    // Redirect to the index or show page with a success message
    return redirect()->route('index')->with('success', 'Book updated successfully!');
}

public function destroy($id)
{
    // Find the book by its ID
    $book = Book::findOrFail($id);

    // Delete the book
    $book->delete();

    // Redirect with a success message
    return redirect()->route('index')->with('success', 'Book deleted successfully!');
}

}

```

# Routes/ web.php

```php

Route :: get('/',[BookController :: class,'index']) ->name('index');
Route::get('/book/{id}/show', [BookController::class, 'show'])->name('book.show');

Route ::get('book/create',[BookController :: class ,'create'] ) -> name ('book.create');
Route :: post('book/store',[BookController :: class ,'store'] ) -> name ('store');
Route :: get('book/edit/{id}',[BookController :: class ,'edit'] ) -> name('books.edit');
Route :: put('book/update/{id}',[BookController :: class ,'update'] ) -> name('books.update');
Route :: delete('book/delete/{id}',[BookController :: class ,'destroy'] ) -> name('delete');

```

# provider/appserviceprovider    (for pagination)

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Pagination\Paginator;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        //
    }

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
{
    Paginator::useBootstrapFive();
}
}

```

# layouts/app.blade.php

```

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@yield('title', 'Book Management')</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
        <div class="container">
            <a class="navbar-brand" href="{{ route('index') }}">Book Management</a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav">
                    <li class="nav-item">
                        <a class="nav-link" href="{{ route('index') }}">Home</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="{{ route('book.create') }}">Add Book</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    <div class="container mt-4">
        @yield('content')
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>

```

# books/show.blade.php

```html
@extends('layouts.app')

@section('title', 'Book Details')

@section('content')
<div class="text-center mb-4">
    <h1 class="text-primary"><i class="fas fa-book"></i> Book Details</h1>
</div>

<!-- Table -->
<div class="card shadow-sm">
    <div class="card-body">
        <table class="table table-hover table-bordered text-center">
            <thead class="table-primary">
                <tr>
                    <th><i class="fas fa-hashtag"></i> ID</th>
                    <th><i class="fas fa-book"></i> Title</th>
                    <th><i class="fas fa-user"></i> Author</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>{{ $books->id }}</td>
                    <td>{{ $books->title }}</td>
                    <td>{{ $books->author }}</td>
                </tr>
            </tbody>
        </table>
    </div>
</div>

<!-- Footer -->
<div class="text-center mt-4">
    <p class="text-muted">
        &copy; 2024 Book Management System | Designed with <i class="fas fa-heart text-danger"></i> by You
    </p>
</div>
@endsection

```

# books/edit.blade.php

```html
@extends('layouts.app')

@section('title', 'Edit Item')

@section('content')
<div class="card mt-4">
    <div class="card-header bg-warning text-white">
        <h4>Edit Item</h4>
    </div>
    <div class="card-body">
        @if ($errors->any())
            <div class="alert alert-danger">
                <ul>
                    @foreach ($errors->all() as $error)
                        <li>{{ $error }}</li>
                    @endforeach
                </ul>
            </div>
        @endif

        <!-- Form for editing item -->
        <form action="{{ route('books.update', $book->id) }}" method="POST">
            @csrf
            @method('PUT') <!-- Specify PUT method -->
            <div class="mb-3">
                <label for="id" class="form-label">ID</label>
                <input type="number" name="id" id="id" class="form-control"
                       value="{{ old('id', $book->id) }}" readonly>
            </div>
            <div class="mb-3">
                <label for="title" class="form-label">Title</label>
                <input type="text" name="title" id="title" class="form-control"
                       value="{{ old('title', $book->title) }}" required>
            </div>
            <div class="mb-3">
                <label for="author" class="form-label">Author</label>
                <input type="text" name="author" id="author" class="form-control"
                       value="{{ old('author', $book->author) }}" required>
            </div>
            <div class="mb-3">
                <label for="description" class="form-label">Description</label>
                <textarea name="description" id="description" class="form-control"
                          rows="4" required>{{ old('description', $book->description) }}</textarea>
            </div>
            <button type="submit" class="btn btn-primary">Update</button>
            <a href="{{ route('index') }}" class="btn btn-secondary">Back</a>
        </form>
    </div>
</div>
@endsection

```

# books/create.blade.php

```html
@extends('layouts.app')

@section('title', 'Add Book')

@section('content')
<h1>Add Book</h1>

@if ($errors->any())
<div class="alert alert-danger">
    <ul>
        @foreach ($errors->all() as $error)
        <li>{{ $error }}</li>
        @endforeach
    </ul>
</div>
@endif

<form action="{{ route('books.store') }}" method="POST">
    @csrf
    <div class="mb-3">
        <label for="id" class="form-label">Book ID</label>
        <input type="number" name="id" id="id" class="form-control" value="{{ old('id') }}">
    </div>
    <div class="mb-3">
        <label for="title" class="form-label">Title</label>
        <input type="text" name="title" id="title" class="form-control" value="{{ old('title') }}">
    </div>
    <div class="mb-3">
        <label for="author" class="form-label">Author</label>
        <input type="text" name="author" id="author" class="form-control" value="{{ old('author') }}">
    </div>
    <div class="mb-3">
        <label for="description" class="form-label">Description</label>
        <textarea name="description" id="description" class="form-control" rows="4">{{ old('description') }}</textarea>
    </div>
    <button type="submit" class="btn btn-success">Save</button>
</form>
@endsection

```

# **books/index.blade.php**

```html
@extends('layouts.app')

@section('title', 'Book List')

@section('content')
<div class="d-flex justify-content-between align-items-center mb-3">
    <h1>Books</h1>
    <form action="{{ route('index') }}" method="GET" class="d-flex">
        <input type="text" name="search" class="form-control me-2" placeholder="Search" value="{{ $search }}">
        <button type="submit" class="btn btn-primary">Search</button>
    </form>
</div>

@if($books->count())
    <table class="table table-striped">
        <thead>
            <tr>
                <th>ID</th>
                <th>Title</th>
                <th>Author</th>
                <th>Description</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody>
            @foreach($books as $book)
            <tr>
                <td>{{ $book->id }}</td>
                <td>{{ $book->title }}</td>
                <td>{{ $book->author }}</td>
                <td>{{ Str::limit($book->description, 50) }}</td>
                <td>
                    <a href="{{ route('book.show', $book->id) }}" class="btn btn-info btn-sm">View</a>
                    <a href="{{ route('books.edit', $book->id) }}" class="btn btn-warning btn-sm">Edit</a>
                    <form action="{{ route('delete', $book->id) }}" method="POST" class="d-inline">
                        @csrf
                        @method('DELETE')
                        <button type="submit" class="btn btn-danger btn-sm" onclick="return confirm('Are you sure?')">Delete</button>
                    </form>
                </td>
            </tr>
            @endforeach
        </tbody>
    </table>
    {{ $books->links() }}
@else
    <p>No books found.</p>
@endif
@endsection

```
