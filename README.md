# Laravel Repository Pattern

How repository pattern can be used in Laravel

This pattern consist of two layers `interface` and `repository`.

* __Interface__

```php
<?php
namespace App\Interfaces;

interface UserInterface
{
    public function index();
    public function store($data);
    public function show($id);
    public function update($data, $id);
    public function destroy($id);
}
```

* __Repository__

```php
<?php
namespace App\Repositories;

use App\User;
use App\Interfaces\UserInterface;

class UserRepository implements UserInterface
{
    protected $user;
    
    public function __construct(User $user)
    {
        $this->user = $user;
    }
    
    public function index()
    {
        $users = $this->user::all();
        return $users;
    }
    
    public function store($data)
    {
        $user = $this->user::create($data);
        return $user;
    }
    
    public function show($id)
    {
        $user = $this->user::findOrFail($id);
        return $user;
    }
    
    public function update($data, $id)
    {
        $user = $this->user::findOrFail($id);
        $user->fill($data);
        $user->save();
        return $user;
    }
    
    public function destroy($id)
    {
        $user = $this->user::findOrFail($id);
        $user->delete();
        return $user;
    }
}
```

* __Interface & Repository bind__

This _Repository_ Pattern won't work if you haven't bind your `Interface` and `Repository` properly. Here in this case an example with step by step is given, how to bind each `Repository` and `Interface`. The binding of `UserInterface.php` and `UserRepository.php` is as follows,

1. Go to App\Providers\AppServiceProvider.php.
2. Add a line `$this->app->bind('App\Interfaces\UserInterface', 'App\Repositories\UserRepository');` inside register method like below given code.

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        $this->app->bind('App\Interfaces\UserInterface', 'App\Repositories\UserRepository');
    }

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        //
    }
}
```

* Use repository function in controller

```php
<?php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Interfaces\UserInterface;

class UserController extends Controller
{
   protected $user;
  
   public function __construct(UserInterface $userInterface)
   {
    $this->user = $userInterface;
   }
  
   public function index()
   {
    $users = $this->user->index();
    return view('users.index')->withUsers($users);
   }
 }
  ```
