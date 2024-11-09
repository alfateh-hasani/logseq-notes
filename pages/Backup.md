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
	      
	      ],
	  ]
	  ```