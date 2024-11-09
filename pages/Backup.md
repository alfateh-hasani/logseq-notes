## Install Spatie backup
	- install lib 
	  ```shell
	  composer require spatie/laravel-backup
	  ```
	- publish backup file
	  
	  ```shd
	  php artisan vendor:publish --provider="Spatie\Backup\BackupServiceProvider"
	  ```
	- setup settings
	  
	  ```php
	  [
	  	'name' => '/',
	    	'exclude' => [
	          base_path('vendor'),
	          base_path('node_modules'),
	          storage_path('backups')
	      ],
	      'mysql' => [
	        'dump' => [
	          'excludeTables' => [
	            'telescope_entries',
	            'telescope_entries_tags',
	            'telescope_monitoring',
	          ],
	        ],
	      ],
	    	'destination' => [
	      'disks' => [
	          'backup',
	          'google',
	        ],
	      ],
	    	'notifications' => [
	      	'mail' => [
	              'to' => 'email@exampl.com',
	  
	              'from' => [
	                  'address' => env('MAIL_FROM_ADDRESS', 'send_email@exampl.com'),
	                  'name' => env('MAIL_FROM_NAME', 'Your name'),
	              ],
	          ],
	      ],
	  ]
	  ```
	- filesystem.php setup
	  
	  ```php
	  	'backup' => [
	          'driver' => 'local',
	          'root' => storage_path('backups'),
	      ],
	  ```
- ## setup Google Drive
	- Install lib
	  
	  ```shell
	  composer require google/apiclient
	  composer require masbug/flysystem-google-drive-ext 
	  ```
	- create provider and code for google
		- ```shell
		  php artisan make:provider GoogleDriveServiceProvider
		  ```
		- ```php
		  <?php
		  
		  namespace App\Providers;
		  
		  use Illuminate\Support\ServiceProvider;
		  use Illuminate\Support\Facades\Storage;
		  use Google\Client;
		  use Google\Service\Drive;
		  use Masbug\Flysystem\GoogleDriveAdapter;
		  
		  class GoogleDriveServiceProvider extends ServiceProvider
		  {
		      public function boot()
		      {
		          Storage::extend('google', function($app, $config) {
		              $client = new Client();
		              $client->setClientId($config['clientId']);
		              $client->setClientSecret($config['clientSecret']);
		              $client->refreshToken($config['refreshToken']);
		  
		              $service = new Drive($client);
		  
		              $options = [
		                  'useHasDir' => true,
		                  'defaultMimeType' => 'application/octet-stream',
		                  'parents' => [$config['folder']],
		              ];
		  
		              $adapter = new GoogleDriveAdapter($service, $config['folder'], $options);
		              $filesystem = new \League\Flysystem\Filesystem($adapter);
		  
		              return new \Illuminate\Filesystem\FilesystemAdapter($filesystem, $adapter, $config);
		          });
		      }
		  }
		  ```
	- setup .env
	  
	  ```.env
	  GOOGLE_DRIVE_CLIENT_ID=xxx.apps.googleusercontent.com
	  GOOGLE_DRIVE_CLIENT_SECRET=
	  GOOGLE_DRIVE_REFRESH_TOKEN=
	  GOOGLE_DRIVE_FOLDER=
	  ```