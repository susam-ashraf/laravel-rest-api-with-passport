# laravel-rest-api-with-passport.

REST API in Laravel with authentication using Passport.

# Project Includes : 
1) Login API
2)Register API
3)Details API

# ----------------------------------------------

In today API is also known as Web services. Web services very important when you are creating web and mobile app developing. You require to create API for your mobile application developer. As we know laravel is more popular because of creating API. But if you are a starter and you don’t know what is API and web services, then you are the right place. In this example, I will show you how to create very simple API and authentication.
What is Passport?
APIs typically use tokens to authenticate users and do not maintain session state between requests. Laravel makes API authentication a breeze using Laravel Passport, which provides a full OAuth2 server implementation for your Laravel application development in a matter of minutes.
You have to just follow a few steps to get following web services
1) Login API
2)Register API
3)Details API
Step 1: Install Laravel
In the first step, we require to get fresh Laravel application using below command, So open your terminal OR command prompt and run below command:
composer create-project --prefer-dist laravel/laravel blog
Step 2: Install Package
composer require laravel/passport
After successfully install package, open config/app.php file and add service provider.
config/app.php
'providers' =>[
Laravel\Passport\PassportServiceProvider::class,
],
Step 3: Run Migration and Install
After Passport service provider registers, we require to run the migration command, after run migration command you will get several new tables in the database. So, let’s run below command:
php artisan migrate
Next, we need to install a passport using the command, Using passport:install command, it will create token keys for security. So let’s run below command:
php artisan passport:install
Step 4: Passport Configuration
In this step, we have to do the configuration on three place model, service provider and auth config file. So you have to just follow change on that file.
In model, we added HasApiTokens class of Passport,
In AuthServiceProvider we added “Passport::routes()”,
In auth.php, we added API auth configuration.
app/User.php
<?php
namespace App;
use Laravel\Passport\HasApiTokens;
use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;
class User extends Authenticatable
{
  use HasApiTokens, Notifiable;
/**
* The attributes that are mass assignable.
*
* @var array
*/
protected $fillable = [
'name', 'email', 'password',
];
/**
* The attributes that should be hidden for arrays.
*
* @var array
*/
protected $hidden = [
'password', 'remember_token',
];
}
app/Providers/AuthServiceProvider.php
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
config/auth.php
<?php
return [
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
Step 5: Create API Route
In this step, we will create API routes. Laravel provides api.php file for write web services route. So, let’s add a new route on that file.
routes/api.php
<?php
/*
|--------------------------------------------------------------------------
| API Routes
|--------------------------------------------------------------------------
|
| Here is where you can register API routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| is assigned the "api" middleware group. Enjoy building your API!
|
*/
Route::post('login', 'API\UserController@login');
Route::post('register', 'API\UserController@register');
Route::group(['middleware' => 'auth:api'], function(){
Route::post('details', 'API\UserController@details');
});
Step 6: Create the Controller
In the last step we have to create a new controller and three API method, So first create a new directory “API” on Controllers folder. So let’s create UserController and put bellow code:
<?php
namespace App\Http\Controllers\API;
use Illuminate\Http\Request; 
use App\Http\Controllers\Controller; 
use App\User; 
use Illuminate\Support\Facades\Auth; 
use Validator;
class UserController extends Controller 
{
public $successStatus = 200;
/** 
     * login api 
     * 
     * @return \Illuminate\Http\Response 
     */ 
    public function login(){ 
        if(Auth::attempt(['email' => request('email'), 'password' => request('password')])){ 
            $user = Auth::user(); 
            $success['token'] =  $user->createToken('MyApp')-> accessToken; 
            return response()->json(['success' => $success], $this-> successStatus); 
        } 
        else{ 
            return response()->json(['error'=>'Unauthorised'], 401); 
        } 
    }
/** 
     * Register api 
     * 
     * @return \Illuminate\Http\Response 
     */ 
    public function register(Request $request) 
    { 
        $validator = Validator::make($request->all(), [ 
            'name' => 'required', 
            'email' => 'required|email', 
            'password' => 'required', 
            'c_password' => 'required|same:password', 
        ]);
if ($validator->fails()) { 
            return response()->json(['error'=>$validator->errors()], 401);            
        }
$input = $request->all(); 
        $input['password'] = bcrypt($input['password']); 
        $user = User::create($input); 
        $success['token'] =  $user->createToken('MyApp')-> accessToken; 
        $success['name'] =  $user->name;
return response()->json(['success'=>$success], $this-> successStatus); 
    }
/** 
     * details api 
     * 
     * @return \Illuminate\Http\Response 
     */ 
    public function details() 
    { 
        $user = Auth::user(); 
        return response()->json(['success' => $user], $this-> successStatus); 
    } 
}
Now we are ready to run our example so run below command to quick run:
php artisan serve
Login API:
Now, we can simple test by rest client tools, So I test it and you can see below screenshot for login API.

Validation of Register API:
Validation is the most important aspect while designing an application. It validates the incoming data. By default, the base controller class uses a ValidatesRequests trait which provides a convenient method to validate incoming HTTP requests with a variety of powerful validation rules.
Laravel Provide very strong validator class when you build API with laravel then use ValidatesRequests trait for validating your request I define Validator class in register function

Register API:

Now, we will test details API, In this api, you have to set two headers as listed below:
‘headers’ => [
‘Accept’ => ‘application/json’,
‘Authorization’ => ‘Bearer ‘.$accessToken,
]
So, make sure above header, otherwise, you can not get user details.
Details API:

