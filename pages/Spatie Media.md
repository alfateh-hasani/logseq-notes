## Installation
	- ### Composer
		- ```shell
		  composer require "spatie/laravel-medialibrary"
		  ```
	- ### Publish
		- Migrations
		  ```shell
		  php artisan vendor:publish --provider="Spatie\MediaLibrary\MediaLibraryServiceProvider" --tag="medialibrary-migrations"
		  ```
			- Migrate
			  
			  ```shell
			  php artisan migrate
			  ```
		- Config
		  
		  ```shell
		  php artisan vendor:publish --provider="Spatie\MediaLibrary\MediaLibraryServiceProvider" --tag="medialibrary-config"
		  ```