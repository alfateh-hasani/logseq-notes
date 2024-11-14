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
	- ## Disk:
		- If you want to change default public disk
		- add disk to config/filesystem.php
		  ```php
		  'media' => [
		    	'driver' => 'local',
		    	'root'   => public_path('media'),
		    	'url'    => env('APP_URL').'/media',
		  ],
		  ```
		- add disk name to config/media-library.php
		  ```php
		  'disk_name' => 'media',
		  ```
- ## Usage
	- ### Prepare Model
		- Implement HasMedia, use trait InteractWithMedia
			- ```php
			  namespace App\Models;
			  
			  use Illuminate\Database\Eloquent\Model;
			  use Spatie\MediaLibrary\HasMedia;
			  use Spatie\MediaLibrary\InteractsWithMedia;
			  
			  class YourModel extends Model implements HasMedia
			  {
			      use InteractsWithMedia;
			  }
			  ```
		- If you want to generate thumbnails, add this to model
			- ```php
			  use Spatie\Image\Enums\Fit;
			  use Spatie\MediaLibrary\MediaCollections\Models\Media;
			  
			  public function registerMediaConversions(?Media $media = null): void
			  {
			      $this
			          ->addMediaConversion('preview')
			          ->fit(Fit::Contain, 300, 300)
			          ->nonQueued();
			  }
			  ```
	- ### Add Images
		- From Request
			- ```php
			  $yourModel->addMediaFromRequest('image')->toMediaCollection('images');
			  ```
-
-
- ## Note
	- If you change the disk and using Git, So add it to .gitignore
	  
	  ```.gitignore
	  ```