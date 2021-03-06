Laravel Eloquent MongoDB [![Build Status](https://travis-ci.org/jenssegers/Laravel-MongoDB.png?branch=master)](https://travis-ci.org/jenssegers/Laravel-MongoDB)
========================

An Eloquent model that supports MongoDB, inspired by LMongo, but using the original Eloquent methods.

*This model extends the original Eloquent model, so it uses exactly the same methods. Some advanced Eloquent features may not be working, but feel free to report them or issue a pull request!*

For more information about Eloquent, check http://laravel.com/docs/eloquent.

Installation
------------

Add the package to your `composer.json` or install manually.

    {
        "require": {
            "jenssegers/mongodb": "*"
        }
    }

Run `composer update` to download and install the package.

Add the service provider in `app/config/app.php`:

    'Jenssegers\Mongodb\MongodbServiceProvider',

Add an alias for the query builder, you can change this alias to your own preference:

    'MDB'            => 'Jenssegers\Mongodb\Facades\DB',

Configuration
-------------

This package will automatically check the database configuration in `app/config/database.php` for a 'mongodb' item.

    'mongodb' => array(
        'host'     => 'localhost',
        'port'     => 27017,
        'username' => 'username',
        'password' => 'password',
        'database' => 'database'
    ),

You can also specify the connection name in the model if you have multiple connections:

    class MyModel extends Eloquent {
    
        protected $connection = 'mongodb2';
    
    }

You can connect to multiple servers or replica sets with the following configuration:

    'mongodb' => array(
        'host'     => array('server1', 'server2),
        'port'     => 27017,
        'username' => 'username',
        'password' => 'password',
        'database' => 'database',
        'options'  => array('replicaSet' => 'replicaSetName')
    ),

Eloquent
--------

Tell your model to use the MongoDB model and set the collection (alias for table) property:
    
    use Jenssegers\Mongodb\Model as Eloquent;
    
    class MyModel extends Eloquent {
    
        protected $collection = 'mycollection';
    
    }

Everything else works just like the original Eloquent model. Read more about the Eloquent on http://laravel.com/docs/eloquent

Query Builder
-------------

The MongoDB query builder allows you to execute queries, just like the original query builder (note that we are using the previously created alias here):

    $users = MDB::collection('users')->get();
    $user = MDB::collection('users')->where('name', 'John')->first();

Read more about the query builder on http://laravel.com/docs/queries

More examples
-------------

**Retrieving All Models**

    $users = User::all();

**Retrieving A Record By Primary Key**

    $user = User::find('517c43667db388101e00000f');

**Wheres**

    $users = User::where('votes', '>', 100)->take(10)->get();

**Or Statements**

    $users = User::where('votes', '>', 100)->orWhere('name', 'John')->get();

**Using Where In With An Array**

    $users = User::whereIn('age', array(16, 18, 20))->get();

When using `whereNotIn` objects will be returned if the field is non existant. Combine with `whereNotNull('age')` to leave out those documents.

**Using Where Between**

    $users = User::whereBetween('votes', array(1, 100))->get();

**Where null**

    $users = User::whereNull('updated_at')->get();

**Order By**

    $users = User::orderBy('name', 'desc')->get();

**Offset & Limit**

    $users = User::skip(10)->take(5)->get();

**Distinct**

Distinct requires a field for which to return the distinct values.

    $users = User::distinct()->get(array('name'));
    // or
    $users = User::distinct('name')->get();

Distinct can be combined with **where**:

    $users = User::where('active', true)->distinct('name')->get();

**Advanced Wheres**

    $users = User::where('name', '=', 'John')->orWhere(function($query)
        {
            $query->where('votes', '>', 100)
                  ->where('title', '<>', 'Admin');
        })
        ->get();

**Group By**

Selected columns that are not grouped will be aggregated with the $last function.

    $users = Users::groupBy('title')->get(array('title', 'name'));

**Aggregation**

    $total = Order::count();
    $price = Order::max('price');
    $price = Order::min('price');
    $price = Order::avg('price');
    $total = Order::sum('price');

Aggregations can be combined with **where**:

    $sold = Orders::where('sold', true)->sum('price');

**Like**

    $user = Comment::where('body', 'like', '%spam%')->get();

**Inserts, updates and deletes**

All basic insert, update, delete and select methods should be implemented.

**Incrementing or decrementing a value of a column**

Perform increments or decrements (default 1) on specified attributes:

    User::where('name', 'John Doe')->increment('age');
    User::where('name', 'Jaques')->decrement('weight', 50);

The number of updated objects is returned:

    $count = User->increment('age');

You may also specify additional columns to update:

    User::where('age', '29')->increment('age', 1, array('group' => 'thirty something'));
    User::where('bmi', 30)->decrement('bmi', 1, array('category' => 'overweight'));

**Raw Expressions**

These expressions will be injected directly into the query.

    User::whereRaw(array('age' => array('$gt' => 30, '$lt' => 40)))->get();

**Query Caching**

You may easily cache the results of a query using the remember method:

    $users = User::remember(10)->get();
