# Install
	- In Laravel 
	  ```shell
	  composer require predis/predis	
	  ```
	- Configure .env
	  
	  ```.env
	  REDIS_HOST=127.0.0.1
	  REDIS_PASSWORD=null
	  REDIS_PORT=6379
	  ```
- # 2- Basic Redis Data Types & Commands
	- ## 1. String
		- ```php
		  class RedisStringExample
		  {
		      private $redis;
		  
		      public function __construct()
		      {
		          $this->redis = Redis::connection();
		      }
		  
		      public function basicStringOperations()
		      {
		          // SET and GET
		          $this->redis->set('user:name', 'John Doe');
		          $name = $this->redis->get('user:name'); // Returns "John Doe"
		  
		          // SET with expiration (in seconds)
		          $this->redis->setex('session:token', 3600, 'abc123');
		  
		          // Multiple operations
		          $this->redis->mset([
		              'user:firstname' => 'John',
		              'user:lastname' => 'Doe'
		          ]);
		  
		          // Increment/Decrement
		          $this->redis->set('visitors', 0);
		          $this->redis->incr('visitors');    // 1
		          $this->redis->incrby('visitors', 10); // 11
		          $this->redis->decr('visitors');    // 10
		      }
		  }
		  ```
	- ## 2. Lists (Ordered collections)
		- ```php
		  class RedisListExample
		  {
		      private $redis;
		  
		      public function __construct()
		      {
		          $this->redis = Redis::connection();
		      }
		  
		      public function listOperations()
		      {
		          // Push items to list
		          $this->redis->lpush('tasks', 'Task 1');  // Left push
		          $this->redis->rpush('tasks', 'Task 2');  // Right push
		  
		          // Pop items from list
		          $task = $this->redis->lpop('tasks');     // Left pop
		          $task = $this->redis->rpop('tasks');     // Right pop
		  
		          // Get list length
		          $length = $this->redis->llen('tasks');
		  
		          // Get range of items
		          $tasks = $this->redis->lrange('tasks', 0, -1); // Get all items
		  
		          // Example: Simple Queue Implementation
		          public function addToQueue($task)
		          {
		              $this->redis->rpush('queue:tasks', json_encode($task));
		          }
		  
		          public function processQueue()
		          {
		              while ($task = $this->redis->lpop('queue:tasks')) {
		                  $taskData = json_decode($task, true);
		                  // Process task...
		              }
		          }
		      }
		  }
		  ```
-
	-