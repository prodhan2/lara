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
use App\Models\Book;
use Faker\Factory as Faker;

class DatabaseSeeder extends Seeder
{
    /**
     * Seed the application's database.
     */
    public function run()
    {
        // Truncate the books table before seeding new data
        Book::truncate();

        $faker = Faker::create();

        // Loop to create 50 fake books
        foreach (range(1, 50) as $index) {
            Book::create([
                'title' => $faker->sentence(3),
                'author' => $faker->name,
                'price' => $faker->randomFloat(2, 5, 100),
                'quantity' => $faker->numberBetween(1, 50),
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

# books/show.blade.php

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Book Details</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
    <!-- Font Awesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
</head>
<body class="bg-light">

<div class="container mt-5">
    <!-- Page Header -->
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
                        <td>{{$books->id}}</td>
                        <td>{{$books->title}}</td>
                        <td>{{$books->author}}</td>
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
</div>

<!-- Bootstrap JS Bundle with Popper -->
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>

```

# books/edit.blade.php

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Edit Item</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <div class="card">
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
    </div>

    <!-- Bootstrap JS Bundle -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>

```

# books/create.blade.php

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Create Item</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <div class="card">
            <div class="card-header bg-primary text-white">
                <h4>Create Item</h4>
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

                <form action="{{ route('store') }}" method="POST">
                    @csrf
                    <div class="mb-3">
                        <label for="id" class="form-label">ID</label>
                        <input type="number" name="id" id="id" class="form-control"
                               placeholder="Enter ID" value="{{ old('id') }}" required>
                    </div>
                    <div class="mb-3">
                        <label for="title" class="form-label">Title</label>
                        <input type="text" name="title" id="title" class="form-control"
                               placeholder="Enter Title" value="{{ old('title') }}" required>
                    </div>
                    <div class="mb-3">
                        <label for="author" class="form-label">Author</label>
                        <input type="text" name="author" id="author" class="form-control"
                               placeholder="Enter Author" value="{{ old('author') }}" required>
                    </div>
                    <div class="mb-3">
                        <label for="description" class="form-label">Description</label>
                        <textarea name="description" id="description" class="form-control"
                                  rows="4" placeholder="Enter Description" required>{{ old('description') }}</textarea>
                    </div>
                    <button type="submit" class="btn btn-success">Submit</button>
                    <a href="{{ route('index') }}" class="btn btn-secondary">Back</a>
                </form>
            </div>
        </div>
    </div>

    <!-- Bootstrap JS Bundle -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>

```

# **books/index.blade.php**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Book List</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
    <!-- Bootstrap Icons -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons/font/bootstrap-icons.css" rel="stylesheet">
</head>
<body class="bg-light">
    <!-- Header Section -->
    <div class="container mt-4">
        <div class="row">
            <div class="col-lg-10">
                <h4>Search Any Book</h4>
            </div>
            <div class="col-lg-2 text-end">
                <a href="{{ route('book.create') }}" class="btn btn-primary">Add Book</a>
            </div>
        </div>
    </div>

    <!-- Search Form Section -->
    <div class="container mt-4">
        <form method="GET" action="{{ route('index') }}" class="row g-3">
            <div class="col-auto">
                <input type="text" name="search" class="form-control"
                       value="{{ request('search') }}" placeholder="Search books...">
            </div>
            <div class="col-auto">
                <button type="submit" class="btn btn-primary">
                    <i class="bi bi-search"></i> Search
                </button>
            </div>
        </form>
    </div>

    <!-- Main Content Section -->
    <div class="container my-5">
        <h1 class="text-center mb-4">Book List</h1>
        <div class="table-responsive">
            <table class="table table-hover align-middle bg-white shadow rounded">
                <thead class="table-dark">
                    <tr>
                        <th scope="col"><i class="bi bi-hash"></i> ID</th>
                        <th scope="col"><i class="bi bi-book"></i> Title</th>
                        <th scope="col"><i class="bi bi-person"></i> Author</th>
                        <th scope="col"><i class="bi bi-gear"></i> Actions</th>
                    </tr>
                </thead>
                <tbody>
                    @forelse ($books as $book)
                        <tr>
                            <td>{{ $book->id }}</td>
                            <td>{{ $book->title }}</td>
                            <td>{{ $book->author }}</td>
                            <td>
                                <a href="{{ route('book.show', $book->id) }}" class="btn btn-info btn-sm text-white">
                                    <i class="bi bi-eye"></i> View
                                </a>
                                <a href="{{ route('books.edit', $book->id) }}" class="btn btn-warning btn-sm text-white">
                                    <i class="bi bi-pencil"></i> Edit
                                </a>
                                <form action="{{ route('delete', $book->id) }}" method="POST" style="display: inline-block;">
                                    @csrf
                                    @method('DELETE')
                                    <button type="submit" class="btn btn-danger btn-sm"
                                            onclick="return confirm('Are you sure you want to delete this book?')">
                                        Delete
                                    </button>
                                </form>
                            </td>
                        </tr>
                    @empty
                        <tr>
                            <td colspan="4" class="text-center">No books found.</td>
                        </tr>
                    @endforelse
                </tbody>
            </table>
        </div>

        <!-- Pagination -->
        <div class="d-flex justify-content-center">
            {{ $books->links() }} <!-- Laravel Pagination links -->
        </div>
    </div>

    <!-- Bootstrap JS (Optional) -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>

```
