`composer create-project --prefer-dist laravel/laravel user`

`php artisan make:auth`

`php artisan migrate`

`https://github.com/laravel/socialite`

To get started with Socialite, use Composer to add the package to your project's dependencies:

    composer require laravel/socialite

### Configuration

After installing the Socialite library, register the Laravel\Socialite\SocialiteServiceProvider in your config/app.php configuration file:

```
'providers' => [
    // Other service providers...

    Laravel\Socialite\SocialiteServiceProvider::class,
],
```

Also, add the `Socialite` facade to the `aliases` array in your `app` configuration file:

```php
'Socialite' => Laravel\Socialite\Facades\Socialite::class,
```

You will also need to add credentials for the OAuth services your application utilizes. These credentials should be placed in your `config/services.php` configuration file, and should use the key `facebook`, `twitter`, `linkedin`, `google`, `github` or `bitbucket`, depending on the providers your application requires. For example:
```php
'github' => [
    'client_id' => 'your-github-app-id',
    'client_secret' => 'your-github-app-secret',
    'redirect' => 'http://your-callback-url',
],
```

### Basic Usage

Next, you are ready to authenticate users! You will need two routes: one for redirecting the user to the OAuth provider, and another for receiving the callback from the provider after authentication. We will access Socialite using the `Socialite` facade:

```php
<?php

namespace App\Http\Controllers\Auth;

use Socialite;

class LoginController extends Controller
{
    /**
     * Redirect the user to the GitHub authentication page.
     *
     * @return Response
     */
    public function redirectToProvider()
    {
        return Socialite::driver('github')->redirect();
    }

    /**
     * Obtain the user information from GitHub.
     *
     * @return Response
     */
    public function handleProviderCallback()
    {
		$github = Socialite::driver('github')->user();

        $user = User::whereEmail($github->getEmail())->first();    

        if (!$user) {
            $user = User::create([
                'email' => $github->getEmail(),
                'name' => $github->getName(),
                'password' => '<no_pass>',
            ]);
        }

        auth()->login($user);

        return redirect()->to('/home');
    }
}
```

Of course, you will need to define routes to your controller methods:

```php
Route::get('login/github', 'Auth\LoginController@redirectToProvider')->name('github');
Route::get('login/github/callback', 'Auth\LoginController@handleProviderCallback');
```


Mejor opción: 

https://blog.damirmiladinov.com/laravel/laravel-5.2-socialite-facebook-login.html#.WQgGnFPytE5

## Official Documentation

In addition to typical, form based authentication, Laravel also provides a simple, convenient way to authenticate with OAuth providers using [Laravel Socialite](https://github.com/laravel/socialite). Socialite currently supports authentication with Facebook, Twitter, LinkedIn, Google, GitHub and Bitbucket.

