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
- ##
- ## 6. Redis Pub/Sub (Publisher/Subscriber)
  
  This is great for real-time features like notifications, chat systems, etc.
	- ```php
	  class RedisPubSubExample
	  {
	      private $redis;
	  
	      public function __construct()
	      {
	          $this->redis = Redis::connection();
	      }
	  
	      // Publisher
	      public function publishNotification($channel, $message)
	      {
	          $this->redis->publish($channel, json_encode($message));
	      }
	  
	      // Subscriber
	      public function subscribeToChannel($channel)
	      {
	          $this->redis->subscribe([$channel], function ($message) {
	              // Handle message
	              $data = json_decode($message);
	              // Process notification...
	          });
	      }
	  
	      // Example: Real-time Notification System
	      public function sendUserNotification($userId, $message)
	      {
	          $notification = [
	              'userId' => $userId,
	              'message' => $message,
	              'timestamp' => time()
	          ];
	          
	          $this->publishNotification("user.notifications.$userId", $notification);
	      }
	  }
	  ```
- ## 7. Redis Transactions
  
  Transactions ensure multiple commands are executed atomically.
	- ```php
	  class RedisTransactionExample
	  {
	      private $redis;
	  
	      public function __construct()
	      {
	          $this->redis = Redis::connection();
	      }
	  
	      public function transferPoints($fromUser, $toUser, $points)
	      {
	          $this->redis->multi(); // Start transaction
	  
	          try {
	              // Deduct points from sender
	              $this->redis->decrby("user:$fromUser:points", $points);
	              
	              // Add points to receiver
	              $this->redis->incrby("user:$toUser:points", $points);
	              
	              // Log transaction
	              $this->redis->rpush('points:transactions', json_encode([
	                  'from' => $fromUser,
	                  'to' => $toUser,
	                  'points' => $points,
	                  'timestamp' => time()
	              ]));
	  
	              $this->redis->exec(); // Execute transaction
	              return true;
	          } catch (\Exception $e) {
	              $this->redis->discard(); // Rollback transaction
	              return false;
	          }
	      }
	  }
	  ```
- ## 8. Redis Cache Implementation
  
  Advanced caching strategies with Redis.
	- ```php
	  class RedisCacheManager
	  {
	      private $redis;
	      private $defaultTTL = 3600; // 1 hour
	  
	      public function __construct()
	      {
	          $this->redis = Redis::connection();
	      }
	  
	      public function remember($key, $callback, $ttl = null)
	      {
	          $value = $this->redis->get($key);
	  
	          if (!is_null($value)) {
	              return unserialize($value);
	          }
	  
	          $value = $callback();
	          $this->put($key, $value, $ttl);
	  
	          return $value;
	      }
	  
	      public function put($key, $value, $ttl = null)
	      {
	          $ttl = $ttl ?? $this->defaultTTL;
	          $this->redis->setex($key, $ttl, serialize($value));
	      }
	  
	      public function forget($key)
	      {
	          $this->redis->del($key);
	      }
	  
	      public function flush()
	      {
	          $this->redis->flushdb();
	      }
	  
	      // Example: Caching Product Data
	      public function getProduct($productId)
	      {
	          $cacheKey = "product:$productId";
	          
	          return $this->remember($cacheKey, function () use ($productId) {
	              return Product::with('categories', 'attributes')
	                           ->find($productId);
	          }, 3600);
	      }
	  
	      // Example: Cache Tags Implementation
	      public function taggedCache($tags, $key, $callback, $ttl = null)
	      {
	          // Store tagged cache references
	          foreach ($tags as $tag) {
	              $this->redis->sadd("tag:$tag", $key);
	          }
	  
	          return $this->remember($key, $callback, $ttl);
	      }
	  
	      public function flushTag($tag)
	      {
	          $keys = $this->redis->smembers("tag:$tag");
	          if (!empty($keys)) {
	              $this->redis->del($keys);
	              $this->redis->del("tag:$tag");
	          }
	      }
	  }
	  ```
- ## 9. Redis Queue Implementation
  
  Implementing a robust queue system with Redis.
	- ```php
	  class RedisQueue
	  {
	      private $redis;
	      private $defaultTimeout = 30; // seconds
	  
	      public function __construct()
	      {
	          $this->redis = Redis::connection();
	      }
	  
	      public function push($queue, $job)
	      {
	          $payload = json_encode([
	              'id' => uniqid(),
	              'job' => $job,
	              'attempts' => 0,
	              'created_at' => time()
	          ]);
	  
	          $this->redis->rpush("queue:$queue", $payload);
	      }
	  
	      public function pop($queue)
	      {
	          // Move job from waiting to processing list
	          $payload = $this->redis->blpop("queue:$queue", $this->defaultTimeout);
	          
	          if (!$payload) {
	              return null;
	          }
	  
	          $job = json_decode($payload[1], true);
	          $job['attempts']++;
	  
	          // Add to processing set with expiration
	          $processingKey = "processing:$queue:{$job['id']}";
	          $this->redis->setex($processingKey, 3600, json_encode($job));
	  
	          return $job;
	      }
	  
	      public function complete($queue, $jobId)
	      {
	          $this->redis->del("processing:$queue:$jobId");
	      }
	  
	      public function retry($queue, $job)
	      {
	          if ($job['attempts'] < 3) {
	              $this->push($queue, $job['job']);
	          } else {
	              $this->failed($queue, $job);
	          }
	      }
	  
	      public function failed($queue, $job)
	      {
	          $this->redis->rpush("failed:$queue", json_encode($job));
	      }
	  
	      // Example: Job Processing
	      public function processJobs($queue)
	      {
	          while (true) {
	              $job = $this->pop($queue);
	              
	              if ($job) {
	                  try {
	                      // Process job
	                      $this->processJob($job);
	                      
	                      // Mark as complete
	                      $this->complete($queue, $job['id']);
	                  } catch (\Exception $e) {
	                      // Handle failure
	                      $this->retry($queue, $job);
	                  }
	              }
	          }
	      }
	  }
	  ```
- ## 10. Redis Rate Limiting
  
  Implement rate limiting for APIs or actions.
	- ```php
	  class RedisRateLimiter
	  {
	      private $redis;
	  
	      public function __construct()
	      {
	          $this->redis = Redis::connection();
	      }
	  
	      public function isAllowed($key, $maxAttempts, $window)
	      {
	          $current = $this->redis->get($key);
	  
	          if (!$current) {
	              $this->redis->setex($key, $window, 1);
	              return true;
	          }
	  
	          if ($current < $maxAttempts) {
	              $this->redis->incr($key);
	              return true;
	          }
	  
	          return false;
	      }
	  
	      // Example: API Rate Limiting
	      public function limitApiRequests($userId, $endpoint)
	      {
	          $key = "ratelimit:api:{$userId}:{$endpoint}";
	          
	          // Allow 100 requests per minute
	          if (!$this->isAllowed($key, 100, 60)) {
	              throw new TooManyRequestsException();
	          }
	      }
	  
	      // Example: Login Attempts
	      public function limitLoginAttempts($userId)
	      {
	          $key = "ratelimit:login:$userId";
	          
	          // Allow 5 attempts per 15 minutes
	          if (!$this->isAllowed($key, 5, 900)) {
	              throw new AccountLockedException();
	          }
	      }
	  }
	  ```
- ## 11. Redis Session Management
  
  Handle user sessions with Redis.
- ```php
  class RedisSessionManager
  {
      private $redis;
      private $sessionLifetime = 3600; // 1 hour
  
      public function __construct()
      {
          $this->redis = Redis::connection();
      }
  
      public function createSession($userId, $data)
      {
          $sessionId = $this->generateSessionId();
          $sessionData = [
              'user_id' => $userId,
              'data' => $data,
              'created_at' => time()
          ];
  
          $this->redis->setex(
              "session:$sessionId", 
              $this->sessionLifetime, 
              json_encode($sessionData)
          );
  
          return $sessionId;
      }
  
      public function getSession($sessionId)
      {
          $session = $this->redis->get("session:$sessionId");
          return $session ? json_decode($session, true) : null;
      }
  
      public function updateSession($sessionId, $data)
      {
          $session = $this->getSession($sessionId);
          if ($session) {
              $session['data'] = array_merge($session['data'], $data);
              $this->redis->setex(
                  "session:$sessionId",
                  $this->sessionLifetime,
                  json_encode($session)
              );
          }
      }
  
      public function destroySession($sessionId)
      {
          $this->redis->del("session:$sessionId");
      }
  
      private function generateSessionId()
      {
          return bin2hex(random_bytes(32));
      }
  }
  ```