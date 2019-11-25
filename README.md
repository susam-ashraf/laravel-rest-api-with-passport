
# laravel Rest Api With Passport.

REST API in Laravel with authentication using Passport

## Project Includes : 

1) Login API
2) Register API
3) Details API

## project Dependencies

-> Laravel v5.8

-> php : 7.1.3 

-> MySql : 5.6.24

## Step 1 : Install Package
```
=> composer require paragonie/random_compat=~2.0

=> composer require laravel/passport=~4.0
```
## Step 2: Run Migration and Install
```
=> php artisan migrate

=> php artisan passport:install
```
## Step 3: Passport Configuration
 
 After running this command, add the **Laravel\Passport\HasApiTokens** trait to your **App\User** model. This trait will provide a few helper methods to your model which allow you to inspect the authenticated user's token and scopes:
 
 ```php
 <?php

namespace App;

use Laravel\Passport\HasApiTokens;
use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use HasApiTokens, Notifiable;
}
```
 
 


### ----------------
Next, you should call the **Passport::routes** method within the **boot** method of your **AuthServiceProvider**. This method will register the routes necessary to issue access tokens and revoke access tokens, clients, and personal access tokens:

```php
<?php

namespace App\Providers;

use Laravel\Passport\Passport;
use Illuminate\Support\Facades\Gate;
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

class AuthServiceProvider extends ServiceProvider
{
    /**
     * The policy mappings for the application.
     *
     * @var array
     */
    protected $policies = [
        'App\Model' => 'App\Policies\ModelPolicy',
    ];

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();

        Passport::routes();
    }
}
```


##3 -----------------

Finally, in your **config/auth.php** configuration file, you should set the driver option of the api authentication guard to passport. This will instruct your application to use Passport's TokenGuard when authenticating incoming API requests:

```php
'guards' => [
    'web' => [
        'driver' => 'session',
        'provider' => 'users',
    ],

    'api' => [
        'driver' => 'passport',
        'provider' => 'users',
    ],
],
```



## Step 4: Create API Route

In this step, we will create API routes. Laravel provides **api.php** file for write web services route. So, let’s add a new route on that file.

```ruby
Route::post('login', 'API\UserController@login');
Route::post('register', 'API\UserController@register');
Route::group(['middleware' => 'auth:api'], function(){
Route::post('details', 'API\UserController@details');
```



