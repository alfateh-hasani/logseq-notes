- [[Elequant]]
- ## Libraries
	- Laravel-medialibrary
	  ```
	  composer require "spatie/laravel-medialibrary"
	  ```
-
- Install Laravel Globally
  composer global require laravel/installer
- create project
  
  ```php
  laravel new example-app
  ```
- ## Start Inertia Project
  
  ```php
  ```
- ```php
  ```
- ## Create Laravel Project
- ### Inertia Project
	- ### inertia
	- server side
		- ```shell
		  composer require inertiajs/inertia-laravel
		  ```
		- ```html
		  <html>
		    <head>
		      <meta charset="utf-8" />
		      <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0" />
		      @vite('resources/js/app.js')
		      @inertiaHead
		    </head>
		    <body>
		      @inertia
		    </body>
		  </html>
		  ```
		- ```shell
		  php artisan inertia:middleware
		  ```
		- ```php
		  ->withMiddleware(function (Middleware $middleware) {
		      $middleware->web(append: [
		          HandleInertiaRequests::class,
		      ]);
		  })
		  ```