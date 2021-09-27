How to setup the laravel with breeze login and permission system
--------------------------------------------------------------------

Install:
---------------
In the Terminal where you like to create your Dev folder:
    curl -s "https://laravel.build/Dev" | bash        

    cd Dev
    code .

Add the PHP myAdmin service to the Dockerfile (docker-compose.yml) e.g. after the MySQL service

    phpmyadmin:
        image: phpmyadmin/phpmyadmin:5
        ports:
            - 8080:80
        links:
            - mysql
        environment:
            PMA_HOST: mysql
            PMA_PORT: 3306
        depends_on:
            mysql:
                condition: service_healthy
        networks:
            - sail

Terminal: 
    sail up -d

---------------------------------------------------------------------------

To write shorter Routes using methds like ('/home', 'HomeController@index')

open up app/Providers/RouteServiceProviders

search for 
// protected $namespace = 'App\\Http\\Controllers';

activate it by deleting the //
 
---------------------------------------------------------------------------

Bootstrap 4:
---------------

    app/Providers/AppServiceProvider.php



        namespace App\Providers;
        use Illuminate\Pagination\Paginator;
        use Illuminate\Support\ServiceProvider;

        ...

        /**
        * Bootstrap any application services.
        *
        * @return void
        */
        public function boot()
        {
            Paginator::useBootstrap();
        }

kleiner Hinweis für Mirko zuhause;


/**
     * Define your route model bindings, pattern filters, etc.
     *
     * @return void
     */
    public function boot()
    {   
        Paginator::useBootstrap();  ---------->>>>> $ use Illuminate\Pagination\Paginator;
        
        $this->configureRateLimiting();

        $this->routes(function () {
            Route::prefix('api')
                ->middleware('api')
                ->namespace($this->namespace)
                ->group(base_path('routes/api.php'));

            Route::middleware('web')
                ->namespace($this->namespace)
                ->group(base_path('routes/web.php'));
        });
    }


---------------------------------------------------------------------------

Sass:

go to /resources
create a sass folder
create your scss files here 
    e.g.
    style.scss
    _variables.scss
    ...

in the webpack.mix.js:

        comment out or delete these lines (not needed)
        mix.js('resources/js/app.js', 'public/js')
        .postCss('resources/css/app.css', 'public/css', [

        ]);

instead we use this;

    mix.sass('resources/sass/style.scss', 'public/css')

To use your stylesheet in a blade:

    <link rel="stylesheet" href="{{ asset('css/style.css') }}">


sail npm install laravel-mix@latest
sail npm run dev 
sail npm run dev /*----> yes again! */


---------------------------------------------------------------------------

Breeze Authentification:


    sail composer require laravel/breeze --dev
    sail artisan breeze:install
    sail npm install && sail npm run dev


---------------------------------------------------------------------------

Spatie Permission System:

    sail composer require spatie/laravel-permission


add to the config/app.php
/* Package Service Providers...*/
         
    Spatie\Permission\PermissionServiceProvider::class,    
    

sail artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"
sail artisan optimize:clear


change in the app/Models/User.php
    class User extends Authenticatable
    {
        use HasApiTokens, HasFactory, Notifiable;

    to 

    class User extends Authenticatable
{
    use HasApiTokens, HasFactory, Notifiable, HasRoles;

    and above insert 
        use Spatie\Permission\Traits\HasRoles;

sail artisan migrate   (if needed use sail artisan migrate:fresh)


****************************************************************************************************************************

Everything that is needed for this project now is installed and rdy to use

****************************************************************************************************************************
****************************************************************************************************************************


to create roles[admin] use:
sail artisan permission:create-role admin

for role editor:
sail artisan permission:create-role editor

for role guest:
sail artisan permission:create-role guest

*******************************
to create a permission ["write post"]
sail artisan permission:create-permission "write post"

for permission "edit post"
sail artisan permission:create-permission "edit post"

for permission "delete post"
sail artisan permission:create-permission "delete post"


///////////////////////////////////////////////////
///////////////////////////////////////////////////
///////////////////////////////////////////////////

create a logout button

<form method="POST" action="{{route('logout')}}">
    @csrf
    <a href="{{ route('logout') }}" onclick="event.preventDefault();" this.closest('form').submit();>Logout</a>
</form>
