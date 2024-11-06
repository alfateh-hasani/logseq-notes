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
- ## 3. Sets (Unordered collections of unique items)
	- ```php
	  class RedisSetExample
	  {
	      private $redis;
	  
	      public function __construct()
	      {
	          $this->redis = Redis::connection();
	      }
	  
	      public function setOperations()
	      {
	          // Add members to set
	          $this->redis->sadd('users:online', 'user1');
	          $this->redis->sadd('users:online', 'user2');
	  
	          // Check if member exists
	          $exists = $this->redis->sismember('users:online', 'user1'); // true
	  
	          // Get all members
	          $onlineUsers = $this->redis->smembers('users:online');
	  
	          // Remove members
	          $this->redis->srem('users:online', 'user1');
	  
	          // Count members
	          $count = $this->redis->scard('users:online');
	  
	          // Set operations
	          $this->redis->sadd('group1', ['user1', 'user2']);
	          $this->redis->sadd('group2', ['user2', 'user3']);
	          
	          // Intersection
	          $commonUsers = $this->redis->sinter(['group1', 'group2']);
	          
	          // Union
	          $allUsers = $this->redis->sunion(['group1', 'group2']);
	      }
	  }
	  ```
- ## 4. Sorted Sets (Ordered collections with scores)
	- ```php
	  class RedisSortedSetExample
	  {
	      private $redis;
	  
	      public function __construct()
	      {
	          $this->redis = Redis::connection();
	      }
	  
	      public function sortedSetOperations()
	      {
	          // Add members with scores
	          $this->redis->zadd('leaderboard', [
	              'player1' => 100,
	              'player2' => 200,
	              'player3' => 150
	          ]);
	  
	          // Get score of member
	          $score = $this->redis->zscore('leaderboard', 'player1');
	  
	          // Get rank of member (0-based)
	          $rank = $this->redis->zrevrank('leaderboard', 'player1');
	  
	          // Get range by score
	          $topPlayers = $this->redis->zrevrangebyscore(
	              'leaderboard', 
	              '+inf',    // Max score
	              '-inf',    // Min score
	              [
	                  'withscores' => true,
	                  'limit' => [0, 10] // Offset, Count
	              ]
	          );
	  
	          // Increment score
	          $this->redis->zincrby('leaderboard', 50, 'player1');
	      }
	  
	      // Example: Real-time Leaderboard
	      public function updatePlayerScore($playerId, $score)
	      {
	          $this->redis->zadd('leaderboard', $score, $playerId);
	      }
	  
	      public function getTopPlayers($limit = 10)
	      {
	          return $this->redis->zrevrange('leaderboard', 0, $limit - 1, 'WITHSCORES');
	      }
	  }
	  ```
- ## 5. Hashes (Maps between string fields and values)
	- ```php
	  class RedisHashExample
	  {
	      private $redis;
	  
	      public function __construct()
	      {
	          $this->redis = Redis::connection();
	      }
	  
	      public function hashOperations()
	      {
	          // Set hash fields
	          $this->redis->hset('user:1', 'name', 'John Doe');
	          $this->redis->hset('user:1', 'email', 'john@example.com');
	  
	          // Set multiple hash fields
	          $this->redis->hmset('user:2', [
	              'name' => 'Jane Doe',
	              'email' => 'jane@example.com',
	              'age' => 25
	          ]);
	  
	          // Get hash field
	          $name = $this->redis->hget('user:1', 'name');
	  
	          // Get all hash fields
	          $userData = $this->redis->hgetall('user:1');
	  
	          // Check if field exists
	          $exists = $this->redis->hexists('user:1', 'email');
	  
	          // Delete field
	          $this->redis->hdel('user:1', 'age');
	  
	          // Increment numeric field
	          $this->redis->hincrby('user:2', 'age', 1);
	      }
	  
	      // Example: Shopping Cart Implementation
	      public function addToCart($userId, $productId, $quantity)
	      {
	          $cartKey = "cart:$userId";
	          $this->redis->hset($cartKey, $productId, $quantity);
	      }
	  
	      public function getCart($userId)
	      {
	          return $this->redis->hgetall("cart:$userId");
	      }
	  
	      public function updateQuantity($userId, $productId, $quantity)
	      {
	          $cartKey = "cart:$userId";
	          $this->redis->hset($cartKey, $productId, $quantity);
	      }
	  
	      public function removeFromCart($userId, $productId)
	      {
	          $cartKey = "cart:$userId";
	          $this->redis->hdel($cartKey, $productId);
	      }
	  }
	  ```