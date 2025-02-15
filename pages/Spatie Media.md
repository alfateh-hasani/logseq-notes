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
			  $yourModel->addMediaFromRequest('image')->toMediaLibrary();
			  ```
		- From Path
			- ```php
			  $yourModel
			     ->addMedia($pathToFile)
			     ->toMediaLibrary();
			  ```
		- From URL
			- ```php
			  $url = 'http://medialibrary.spatie.be/assets/images/mountain.jpg';
			  $yourModel
			     ->addMediaFromUrl($url)
			     ->toMediaLibrary();
			  ```
		- Adding properties
			- using name, using file name
			- ```php
			  ->usingName('normal name field')
			  ->usingFileName('file name with extention')
			  ```
			- add custom properties like association array
			- ```php
			  ->withCustomProperties([
			    'location' => 'Dublin, Ireland',
			    'subject' => 'Library',
			  ])
			  ```
	- ### Deleting Images
		- ```php
		  Model::all()->each->delete();
		  Model::last()->delete();
		  ```
		- Wrong usage
		- ```php
		  Model::where('category_id',1)->delete();
		  ```
	- ### Collections
		- Add to collection
		- ```php
		  ->toMediaCollection('collection name')
		  ```
		- Add to other disk
		- ```php
		  ->toMediaCollection('collection name', 'disk name');
		  ->toMediaCollection('', 's3')
		  ```
		- retrieve images by collections
		- ```php
		  Model::last()->getMedia('collection name');
		  Model::last()->getFirstMedia('collection name');	
		  ```
		- Register collection in model
		- ```php
		  ```
	- Download File
		- When return it change to http response to be downloadable
		- ```php
		  $media = Model::last()->getMedia();
		  return $media;
		  ```
		- Download many files
		- ```php
		  Media::truncate();
		  
		  Model::create()
		    ->addMedia(storage_path('path'))
		    ->toMediaCollection();
		  
		  Model::create()
		    ->addMedia(storage_path('path'))
		    ->toMediaCollection('downloads', 's3');
		  
		  return MediaStream::create('file name.zip')->addMedia(Media::all());
		  ```
- ## Note
	- If you change the disk and using Git, So add it to .gitignore
	  
	  ```.gitignore
	  ```