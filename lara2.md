Run  a laravel project
To run  Laravel project, follow these steps:  

1. **Clone the Repository**:
    
    ```bash
    
    git clone https://github.com/
    
    ```
    
2. **Install Dependencies**:
Ensure you have Composer installed, then run:
    
    ```bash
    
    composer install
    
    ```
    
3. **Configure Environment**:
Copy the `.env.example` file to create a `.env` file:
    
    ```bash
    
    cp .env.example .env
    
    ```
    
    Open the `.env` file and set up your database configuration:
    
    ```
    
    DB_CONNECTION=mysql
    DB_HOST=127.0.0.1
    DB_PORT=3306
    DB_DATABASE=your_database_name
    DB_USERNAME=your_username
    DB_PASSWORD=your_password
    
    ```
    
4. **Generate Application Key**:
Run the following command to generate a unique application key:
    
    ```bash
    
    php artisan key:generate
    
    ```
    
5. **Set Up the Database**:
Make sure you have created the specified database. Then run the migrations to set up tables:
    
    ```bash
    php artisan migrate
    
    ```
    
6. **Seed the Database (Optional)**:
If the project includes seeding data, run:
    
    ```bash
    
    php artisan db:seed
    
    ```
    
7. **Start the Development Server**:
To run the project locally, start the Laravel development server:
    
    ```bash
    
    php artisan serve
    
    ```
    
8. **Access the Application**:
Open a browser and go to:
    
    ```arduino
    
    http://127.0.0.1:8000
    
    ```
    

After following these steps, the Inventory Management System should be running locally. Let me know if you need more help!

**Run PHP Built-in Server**:
Use the following command to start the PHP server:

```bash

php -S localhost:8000 -t public

```

- `localhost:8000` specifies the host and port.
- `t public` sets the `public` directory as the document root (required for Laravel).


1. Install LARAVEL , Create Project

### Step 1: Install Laravel

1. **Install Composer** (if you haven’t already). Composer is a dependency manager for PHP, necessary to install Laravel.
    
    ```bash
    curl -sS https://getcomposer.org/installer | php
    mv composer.phar /usr/local/bin/composer
    ```
    
2. **Install Laravel** by creating a new Laravel project through Composer.
    
    ```bash
    composer create-project laravel/laravel ProjectName
    ```
    
3. **Navigate to the Project Directory**.
    
    ```bash
    cd ProjectName
    ```
    
4. **Run the Laravel Development Server** to check if the installation was successful.
    
    ```bash
    php artisan serve
    ```
    
    Now, open a browser and go to `http://localhost:8000`. You should see the default Laravel welcome page.
Update DB credential .env file

### Step 3: Configure the Database

1. **Set up the Database in `.env` File**. Update the following values according to your database setup:
    
    ```
    
    DB_CONNECTION=mysql
    DB_HOST=127.0.0.1
    DB_PORT=3306
    DB_DATABASE=your_database
    DB_USERNAME=your_username
    DB_PASSWORD=your_password
    
    ```
    
2. **Create the Database**: Make sure the database exists before proceeding. You can create it manually or through a command line
    
    ```
    php artisan migrate
    ```
4. Create books table (migration)

### **Command to Create the Migration**

Run the following command:

```bash
php artisan make:migration create_books_table
```

---

### **Edit the Migration File**

Navigate to the `database/migrations` folder and open the newly created migration file. Define the schema for the `books` table. Here's an example:

```php

<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('books', function (Blueprint $table) {
            $table->id();
            $table->string('title'); // Book title
            $table->string('author',length:255);
            $table->string('isbn',length:13); // Author name
            $table->decimal('price', 8, 2)->nullable(); // Book price
            $table->integer('stock')->default(0); // Stock quantity
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('books');
    }
};

```

---

### **Run the Migration**

Apply the migration to create the `books` table in the database:

```bash

php artisan migrate

```

For Rollback

```bash

php artisan migrate:rollback

```
5. Create Book Model and factory
### **Step 1: Generate the Book Model and factory**

Run the following Artisan command to create the `Book` model:

```bash

php artisan make:model Book -f


6. Populated books table with test data (Using Factory and Seeder)


Factory method e data defination thakbe

Factory method use kore database e data seed kore …

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/34f39e08-a57b-4416-8568-f2e56d5fe97e/1057a807-0b85-470f-8a58-11dbc275f234/image.png)

To create a **BookFactory** for generating fake data for the `books` table, you can use Laravel's Faker library to define realistic data for each column in the table. Below is the complete code for your `BookFactory`:

---

### **Updated BookFactory**

```php
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

/**
 * @extends \Illuminate\Database\Eloquent\Factories\Factory<\App\Models\Book>
 */
class BookFactory extends Factory
{
    /**
     * Define the model's default state.
     *
     * @return array<string, mixed>
     */
    public function definition(): array
    {
        return [
            'title' => $this->faker->sentence(3), // Random book title
            'author' => $this->faker->name(), // Random author name
            'isbn' => $this->faker->isbn13(), // Random
            'price' => $this->faker->randomFloat(2,10,500),
            'stock' => $this->faker->numberBetween(5,100),
        ];
    }
}

```

Update DatabaseSeeder.php

```php
<?php

namespace Database\Seeders;

use App\Models\User;
use App\Models\Book;
// use Illuminate\Database\Console\Seeds\WithoutModelEvents;
use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    /**
     * Seed the application's database.
     */
    public function run(): void
    {
        User::truncate(); //Delete previous data of database
        User::factory(5)->create();

        // User::factory()->create([
        //     'name' => 'Test User',
        //     'email' => 'test@example.com',
        // ]);
        
        Book::truncate();
        Book::factory(2000)->create();
    }
}

```

db:seed  `db:seed` command:

7. Create Book Controller
To create a **BookController** for managing `Book` resources, follow these steps:

---

### **Step 1: Generate the Controller**

Run the following Artisan command:

```bash
php artisan make:controller BookController
```

This will create a controller in the `app/Http/Controllers` directory with methods for basic CRUD operations.

---

### **Step 2: Define CRUD Methods**

Open the generated `BookController.php` file and implement the methods as needed.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/34f39e08-a57b-4416-8568-f2e56d5fe97e/20664768-0f73-4d40-a8d1-3d5107331eec/image.png)

### Example Implementation:

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Book;

class BookController extends Controller
{
    //index controller
    public function index(){
       $books = Book::all();
       return view("books.index")
        ->with('books',$books);
    }

    //for showing book information
    public function show($id){
        $book = Book::find($id);
        return view('books.show')
        ->with('book',$book);
    }
}

```

---

### **Step 3: Set Up Routes**

Define the resourceful routes in `routes/web.php`:

```php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\BookController;

//for index controller
Route::get('/', [BookController::class, 'index']) -> name('books.index') ;

//for showing book information
Route::get('books/show/{id}', [BookController::class, 'show'])->name('books.show') ;

```

---

### **Step 4: Create Views**

You'll need to create Blade templates for the following views in the `resources/views/books` directory:

1. `index.blade.php` - List all books.
2. `show.blade.php` - Display details of a single book.
3. `create.blade.php` - Form to create a new book.
4. `edit.blade.php` - Form to edit an existing book.

---

1. `index.blade.php` - List all books.

```bash
php artisan make:view books.index
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Book List</title>
</head>
<body>
    <h2>Book List</h2>
    <table class ='table table-striped'>
        <tr>
        <th>ID </th>
        <th>Title</th>
        <th>Author</th>
        <th>ISBN</th>
        <th>Stock</th>
        <th>Price</th>
        <th>Action</th>

        </tr>

        @foreach ($books as $book)
            <tr>
                <td>{{$book->id}}</td>
                <td>{{$book->title}}</td>
                <td>{{$book->author}}</td>
                <td>{{$book->isbn}}</td>
                <td>{{$book->stock}}</td>
                <td>{{$book->price}}</td>
                <td>
                    <a href="{{url('books/'.'show/'.$book->id)}}"> View </a>
                </td>
            </tr>
        @endforeach

    </table>

</body>
</html>

```

1. Create show`.blade.php` - List all books.

```bash
php artisan make:view books.show
```

> Update Book Controller and Route
> 

 show`.blade.php`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Books</title>
</head>
<body>
		<p>
        <a href="{{url('books/')}}"> Back </a>
    </p>
    
    <h2>Book List</h2>
    <table width="25%" border="2">
        <tr>
            <th>ID </th>
            <td>{{$book->id}}</td>
        </tr>

        <tr>
            <th>Title </th>
            <td>{{$book->title}}</td>
        </tr>

        <tr>
            <th>Author</th>
            <td>{{$book->author}}</td>
        </tr>

        <tr>
            <th>ISBN </th>
            <td>{{$book->isbn}}</td>
        </tr>

        <tr>
            <th>Stock </th>
            <td>{{$book->stock}}</td>
        </tr>

        <tr>
            <th>Price</th>
            <td>{{$book->price}}</td>
        </tr>

    </table>

</body>
</html>

```

### **Step 5: Test the Controller**

- Run your Laravel app:
    
    ```bash
    
    php artisan serve
    
    ```
    Using Bootstarp with common layout

### **Step 1: Create a Common Layout**

Create a common layout file in the `resources/views/layouts` directory.

### Example: `resources/views/layout.blade.php`

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Book Store | {{$title}}</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
  </head>
  <body>
    <div class="container">
        @yield('page-content')
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
  </body>
</html>

```

---

### **Step 3: Extend the Layout in Views**

Use the `@extends` directive in your views to include the layout.

### Example: `resources/views/books/index.blade.php`

```html
@extends('layout', ['title' => 'Home'])

    @section('page-content')
    <h2>Book List</h2>

    <div class="row mt-2">

        <div class="col-lg-10">
            Scarch Functionality
        </div>

        <div class="col-lg-2">
            <p class="text-end">
                Add Book
            </p>
        </div>

    </div>

    <table class ='table table-striped'>
        <tr>
        <th>ID </th>
        <th>Title</th>
        <th>Author</th>
        <th>ISBN</th>
        <th>Stock</th>
        <th>Price</th>
        <th>Action</th>

        </tr>

        @foreach ($books as $book)
            <tr>
                <td>{{$book->id}}</td>
                <td>{{$book->title}}</td>
                <td>{{$book->author}}</td>
                <td>{{$book->isbn}}</td>
                <td>{{$book->stock}}</td>
                <td>{{$book->price}}</td>
                <td>
                    <a href="{{url('books/'.'show/'.$book->id)}}"> View </a>
                    <a href="{{url('books/'.'edit/'.$book->id)}}"> Edit </a>
                </td>
            </tr>

        @endforeach

    </table>
    {{$books->links()}}
    @endsection

```

---

### **Step 3: Extend the Layout in Views**

Use the `@extends` directive in your views to include the layout.

### Example: `resources/views/books/show.blade.php`

```html
@extends('layout',['title'=>'Show'])

    @section('page-content')
    <p>
        <a href="{{route('books.index')}}">Back</a>
    </p>
    <h2>Book Details</h2>
    <table class ='table table-striped'>
        <tr>
            <th>ID </th>
            <td>{{$book->id}}</td>
        </tr>

        <tr>
            <th>Title </th>
            <td>{{$book->title}}</td>
        </tr>

        <tr>
            <th>Author</th>
            <td>{{$book->author}}</td>
        </tr>

        <tr>
            <th>ISBN </th>
            <td>{{$book->isbn}}</td>
        </tr>

        <tr>
            <th>Stock </th>
            <td>{{$book->stock}}</td>
        </tr>

        <tr>
            <th>Price</th>
            <td>{{$book->price}}</td>
        </tr>

    </table>
    @endsection

```

### For Pagination

**Step: 01** Ensure you have resourceful routes in app/provider/AppServiceProvide.php:

```php

use Illuminate\Pagination\Paginator;
public function boot(): void
    {
        Paginator::useBootstrapFive();
    }
```

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/34f39e08-a57b-4416-8568-f2e56d5fe97e/f95e47c8-405b-418f-802f-6837f1e9a437/image.png)

**Step:02** Add this in the index.blade.php under </table>

```php
 {{$books->links()}}
```

**Step: 03** BookController.php

```php
 
 //upade this in the index()
 $books = Book::paginate(10);
```

---

Part :2

Add new Book with validation 




    //for adding new book
    public function create(){
        return view('books.create');
    }
    //for store book information
    public function store(Request $request){


        $rules=[
            'title'=> 'required',
            'author'=> 'required',
            'isbn'=> 'required|digits:13',
            'price'=> 'required|numeric|min:0',
           'stock'=> 'required|integer|min:0',
        ];

        $request->validate($rules);

        $book = new Book();
        $book->title = $request->title;
        $book->author = $request->author;
        $book->isbn = $request->isbn;
        $book->price = $request->price;
        $book->stock = $request->stock;
        $book->save();

        //return redirect()->route('books.index');
        return redirect()->route('books.show' ,$book->id);



    }

Edit book

    //for editing book information
    public function edit($id){
        $book = Book::findOrFail($id);
        return view('books.edit')
            ->with('book',$book);

    }
    public function update(Request $request){
        $rules=[
            'title'=> 'required',
            'author'=> 'required',
            'isbn'=> 'required|digits:13',
            'price'=> 'required|numeric|min:0',
           'stock'=> 'required|integer|min:0',
        ];

        $request->validate($rules);

        $book = Book::findOrFail( $request->id );
        $book->title = $request->title;
        $book->author = $request->author;
        $book->isbn = $request->isbn;
        $book->price = $request->price;
        $book->stock = $request->stock;
        $book->save();

        return redirect()->route('books.show' ,$book->id);

    }


Delete Book






BookController.php

```php
 //for deleting book
    public function destroy(Request $request){
        $book = Book::findOrFail($request ->id);
        $book->delete();
        return redirect()->route('books.index');
    }
```

Route > web.php

```php
//for deleting a book
Route::delete('books/delete', [BookController::class, 'destroy'])->name('books.destroy') ;
```

---

### **Extend the Layout in Index**

Use the `@extends` directive in your views to include the layout.

### Example: `resources/views/books/index.blade.php`

```html

//under edit action for delete a book
<form method="post" action="{{route('books.destroy' )}}" onsubmit="return confirm ('Are you sure to delete this book ?') ">
@csrf
@method('DELETE')
<input type="hidden" name="id" value="{{$book->id}}">
<input type="submit" value="Delete" class="btn btn-link"></input>
 </form>
```

---
Scarch
BookController.php

```php
//index function edit korte hbe
//index controller
    public function index(Request $request ){

        if($request->has("scarch")){
            $books=Book::query()
                ->where('title','like','%'.$request->get('scarch').'%')
                ->orWhere('author','like','%'.$request->get('scarch').'%')
                ->paginate(10);
        }
        else{

            $books = Book::paginate(10);
        }

       return view("books.index")
        ->with('books',$books);
    }

```

### **Extend the Layout in Index**

Use the `@extends` directive in your views to include the layout.

### Example: `resources/views/books/index.blade.php`

```php
//Scarch Functionality te replace

<div class="row mt-2">

        <div class="col-lg-10">
            <form method="get" action="{{route('books.index')}}">

                <div class="row g-3">
                    <div class="col">
                      <input type="text"  name="scarch" class="form-control" placeholder="Scarch" >
                    </div>
                    <div class="col">
                        <button type="submit" class="btn btn-success">Scarch</button>
                    </div>
                  </div>

            </form>
        </div>
```



Complete Project
BookController.php

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Book;
use Mockery\Generator\Parameter;

class BookController extends Controller
{
    //index controller
    public function index(Request $request ){

        if($request->has("scarch")){
            $books=Book::query()
                ->where('title','like','%'.$request->get('scarch').'%')
                ->orWhere('author','like','%'.$request->get('scarch').'%')
                ->paginate(10);
        }
        else{

            $books = Book::paginate(10);
        }

       return view("books.index")
        ->with('books',$books);
    }

    //for showing book information
    public function show($id){
        $book = Book::find($id);
        return view('books.show')
        ->with('book',$book);
    }

    //for adding new book
    public function create(){
        return view('books.create');
    }
    //for store book information
    public function store(Request $request){

        $rules=[
            'title'=> 'required',
            'author'=> 'required',
            'isbn'=> 'required|digits:13',
            'price'=> 'required|numeric|min:0',
           'stock'=> 'required|integer|min:0',
        ];

        $request->validate($rules);

        $book = new Book();
        $book->title = $request->title;
        $book->author = $request->author;
        $book->isbn = $request->isbn;
        $book->price = $request->price;
        $book->stock = $request->stock;
        $book->save();

        //return redirect()->route('books.index');
        return redirect()->route('books.show' ,$book->id);
    }

    //for editing book information
    public function edit($id){
        $book = Book::findOrFail($id);
        return view('books.edit')
            ->with('book',$book);

    }
    public function update(Request $request){
        $rules=[
            'title'=> 'required',
            'author'=> 'required',
            'isbn'=> 'required|digits:13',
            'price'=> 'required|numeric|min:0',
           'stock'=> 'required|integer|min:0',
        ];

        $request->validate($rules);

        $book = Book::findOrFail( $request->id );
        $book->title = $request->title;
        $book->author = $request->author;
        $book->isbn = $request->isbn;
        $book->price = $request->price;
        $book->stock = $request->stock;
        $book->save();

        return redirect()->route('books.show' ,$book->id);

    }

    //for deleting book
    public function destroy(Request $request){
        $book = Book::findOrFail($request ->id);
        $book->delete();
        return redirect()->route('books.index');
    }
}

```

Route > web.php

```php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\BookController;

//for index controller
Route::get('/', [BookController::class, 'index']) -> name('books.index') ;

//for showing book information
Route::get('books/show/{id}', [BookController::class, 'show'])->name('books.show') ;
//for creating a new book
Route::get('books/create', [BookController::class, 'create'])->name('books.create') ;
//for storing a new book data into the database
Route::post('/books', [BookController::class, 'store'])->name('books.store') ;

//for editing a book
Route::get('books/edit/{id}', [BookController::class, 'edit'])->name('books.edit') ;
Route::post('books/update', [BookController::class,'update'])->name('books.update') ;

//for deleting a book
Route::delete('books/delete', [BookController::class, 'destroy'])->name('books.destroy') ;

```

### **Common Layout**

Create a common layout file in the `resources/views/layouts` directory.

### Example: `resources/views/layout.blade.php`

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Book Store | {{$title}}</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.3/font/bootstrap-icons.min.css">
  </head>
  <body>
    <div class="container">
        @yield('page-content')
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
  </body>
</html>

```

---

### **Extend the Layout in Index**

Use the `@extends` directive in your views to include the layout.

### Example: `resources/views/books/index.blade.php`

```html
@extends('layout', ['title' => 'Home'])

    @section('page-content')
    <h2>Book List</h2>

    <div class="row mt-2">

        <div class="col-lg-10">
            <form method="get" action="{{route('books.index')}}">

                <div class="row g-3">
                    <div class="col">
                      <input type="text"  name="scarch" class="form-control" placeholder="Scarch" >
                    </div>
                    <div class="col">
                        <button type="submit" class="btn btn-success"> <i class="bi bi-search"></i> Scarch</button>
                    </div>
                  </div>

            </form>
        </div>

        <div class="col-lg-2">
            <p class="text-end">
                <a href="{{route('books.create')}}" class="btn btn-primary"> <i class="bi bi-file-earmark-plus-fill"></i> Add Book</a>
            </p>
        </div>

    </div>

    <table class ='table table-striped'>
        <tr>
        <th>ID </th>
        <th>Title</th>
        <th>Author</th>
        <th>ISBN</th>
        <th>Stock</th>
        <th>Price</th>
        <th>Action</th>

        </tr>

        @foreach ($books as $book)
            <tr>
                <td>{{$book->id}}</td>
                <td>{{$book->title}}</td>
                <td>{{$book->author}}</td>
                <td>{{$book->isbn}}</td>
                <td>{{$book->stock}}</td>
                <td>{{$book->price}}</td>
                <td>
                    <a href="{{url('books/'.'show/'.$book->id)}}"> View </a>
                    <a href="{{url('books/'.'edit/'.$book->id)}}"> Edit </a>

                    <form method="post" action="{{route('books.destroy' )}}" onsubmit="return confirm ('Are you sure to delete this book ?') ">
                        @csrf
                        @method('DELETE')
                        <input type="hidden" name="id" value="{{$book->id}}">
                        <input type="submit" value="Delete" class="btn btn-link"></input>
                    </form>

                </td>
            </tr>

        @endforeach

    </table>
    {{$books->links()}}
    @endsection

```

---

### **Extend the Layout in Views**

Use the `@extends` directive in your views to include the layout.

### Example: `resources/views/books/show.blade.php`

```html
@extends('layout',['title'=>'Show'])

    @section('page-content')
    <p>
        <a href="{{route('books.index')}}">Back</a>
    </p>
    <h2>Book Details</h2>
    <table class ='table table-striped'>
        <tr>
            <th>ID </th>
            <td>{{$book->id}}</td>
        </tr>

        <tr>
            <th>Title </th>
            <td>{{$book->title}}</td>
        </tr>

        <tr>
            <th>Author</th>
            <td>{{$book->author}}</td>
        </tr>

        <tr>
            <th>ISBN </th>
            <td>{{$book->isbn}}</td>
        </tr>

        <tr>
            <th>Stock </th>
            <td>{{$book->stock}}</td>
        </tr>

        <tr>
            <th>Price</th>
            <td>{{$book->price}}</td>
        </tr>

        <tr>
            <th>Created At</th>
            <td>{{$book->created_at}}</td>
        </tr>

        <tr>
            <th>Updated At</th>
            <td>{{$book->updated_at}}</td>
        </tr>

        <tr>
            <th>Updated At</th>
            <td>{{$book->updated_at}}</td>
        </tr>

        <tr>
            <td>
                <a class="btn btn-danger" href="{{url('books/'.'edit/'.$book->id)}}"> <i class="bi bi-pencil-square"></i> Edit </a>
            </td>

        </tr>

    </table>
    @endsection

```

---

### **Adding New Book**

Use the `@extends` directive in your views to include the layout.

### Example: `resources/views/books/create.blade.php`

```php
@extends('layout',['title'=>'Create'])

    @section('page-content')

    <form method="post" action="{{route('books.store')}}">
        @csrf
        <div class="mb-3">
            <label  class="form-label">Title</label>
            <input type="text" class="form-control" name="title" placeholder="Title" value="{{old('title')}}">

            @error('title')
            <div class="text-danger">{{ $message }}</div>
            @enderror

          </div>

          <div class="mb-3">
            <label  class="form-label">Author</label>
            <input type="text" class="form-control" name="author" placeholder="Author" value="{{old('author')}}">
            @error('author')
            <div class="text-danger">{{ $message }}</div>
            @enderror
          </div>

          <div class="mb-3">
            <label  class="form-label">ISBN</label>
            <input type="text" class="form-control" name="isbn" placeholder="ISBN" value="{{old('isbn')}}">

            @error('isbn')
            <div class="text-danger">{{ $message }}</div>
            @enderror

          </div>

          <div class="mb-3">
            <label  class="form-label">Stock</label>
            <input type="text" class="form-control" name="stock" placeholder="Stock" value="{{old('stock')}}">

            @error('stock')
            <div class="text-danger">{{ $message }}</div>
            @enderror

          </div>

          <div class="mb-3">
            <label  class="form-label">Price</label>
            <input type="text" class="form-control" name="price" placeholder="Price"  value="{{old('price')}}">

            @error('price')
            <div class="text-danger">{{ $message }}</div>
            @enderror

          </div>

          <div class="mb-3">
            <button type="submit" class="btn btn-primary"> <i class="bi bi-check2-circle"></i> Submit</button>
            <a href="{{route('books.index')}}" class="btn btn-danger"> <i class="bi bi-arrow-90deg-left"></i> Back</a>
          </div>

    </form>

    @endsection

```

### **Editing BookBook**

Use the `@extends` directive in your views to include the layout.

### Example: `resources/views/books/edit.blade.php`

```php
@extends('layout',['title'=>'Create'])

    @section('page-content')

    <form method="post" action="{{route('books.update')}}">
        @csrf

        <input type="hidden" name="id" value="{{$book->id}}">

        <div class="mb-3">
            <label  class="form-label">Title</label>
            <input type="text" class="form-control" name="title" placeholder="Title" value="{{old('title',$book->title)}}">

            @error('title')
            <div class="text-danger">{{ $message }}</div>
            @enderror

          </div>

          <div class="mb-3">
            <label  class="form-label">Author</label>
            <input type="text" class="form-control" name="author" placeholder="Author" value="{{old('author',$book->author)}}">
            @error('author')
            <div class="text-danger">{{ $message }}</div>
            @enderror
          </div>

          <div class="mb-3">
            <label  class="form-label">ISBN</label>
            <input type="text" class="form-control" name="isbn" placeholder="ISBN" value="{{old('isbn',$book->isbn)}}">

            @error('isbn')
            <div class="text-danger">{{ $message }}</div>
            @enderror

          </div>

          <div class="mb-3">
            <label  class="form-label">Stock</label>
            <input type="text" class="form-control" name="stock" placeholder="Stock" value="{{old('stock',$book->stock)}}">

            @error('stock')
            <div class="text-danger">{{ $message }}</div>
            @enderror

          </div>

          <div class="mb-3">
            <label  class="form-label">Price</label>
            <input type="text" class="form-control" name="price" placeholder="Price"  value="{{old('price',$book->price)}}">

            @error('price')
            <div class="text-danger">{{ $message }}</div>
            @enderror

          </div>

          <div class="mb-3">
            <button type="submit" class="btn btn-primary"> <i class="bi bi-check2-circle"></i> Submit</button>
            <a href="{{route('books.index')}}" class="btn btn-danger"> <i class="bi bi-arrow-90deg-left"> </i> Back</a>
          </div>

    </form>

    @endsection

```



