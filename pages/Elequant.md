# Test Elequant Queries
	- ## Libraries
		- Laravel N+1 Query Detectory
		  
		  ```php
		  composer require beyondcode/laravel-query-detector --dev
		  ```
	- ## Prevent N+1
	- In AppServiceProvider in boot fun
	  
	  ```php
	  Model:: preventLazyLoading(! $this->app->isProduct());
	  ```
	- ## N+1 Problem
		- Not using eager loading
		- ### Eager loading
		- F.X 1
		  ```php
		  $posts = Posts::all();
		  // In blade 
		  {{ $post->user->name }}
		  ```
		- Fix
		  
		  ```php
		  $posts = Posts::with('user')->get();
		  // In blade 
		  {{ $post->user->name }}
		  ```
		- F.X 2
		  ```php
		  $posts = Users::with('post')->get();
		  // In blade 
		  {{ $user->post->count() }}
		  ```
		- Fix
		  
		  ```php
		  $posts = Users::withCount('post')->get();
		  // In blade 
		  {{ $user->post_count }}
		  ```
		- ### Without Eager Loading
		- if data come from api or other place and could not use with
		- F.X 3
		  
		  ```php
		  $posts = Post::all();
		  $posts->load('user');
		  ```
		- ### use Global With parameter in Model
		- ```php
		  class Post extends Model {
		  	protected $with = ['user'];
		  }
		  ```
	- ## Big amount of data
		- F.X
		  ```php
		  $users = User::with('comments')->get();
		  // In blade
		  {{ $user->comments->count() }}
		  ```
		- Fix
		  
		  ```php
		  $users = User::withCount('comments')->get();
		  // In blade
		  {{ $user->comments_count }}
		  ```
		- F.X 
		  
		  ```php
		  $posts = Post::with('ratings')->get();
		  // In blade
		  {{ $post->ratings->count() }}
		  {{ number_format($post->ratings->avg('rating'), 2) }}
		  {{ $post->ratings->max('rating') }}
		  {{ $post->ratings->min('rating') }}
		  ```
		- Fix
		  
		  ```php
		  $posts = Post::withCount('ratings')
		    	->withAvg('ratings', 'rating')
		    	->withMax('ratings', 'rating')
		    	->withMin('ratings', 'rating')
		    ->get();
		  // In blade
		  {{ $post->ratings->count() }}
		  {{ number_format($post->ratings_avg_rating, 2) }}
		  {{ $post->ratings_max_rating }}
		  {{ $post->ratings_min_rating }}
		  ```
	- ## Select only needed columns
		- F.X
		  
		  ```php
		  $posts = Post::select('title', 'user_id')->with('user:id,name,email')->get();
		  ```
	- ## Use query for filtering
		- use query without get() from the beginning to reduce amount of data in time.
		- FX
		  
		  ```php
		  $query = Listing::query()
		    	->where('is_active', true)
		    	->with('tags')
		    	->latest();
		  
		  // code for filter 1
		  $name = trim($request->get('search'));
		  $query->where('name', 'like' , "%{$name}%")
		  // code for filter 2
		  $tag = trim($request->get('tag'));
		  $query->where('slug', 'like' , "%{$tag}%")
		    
		   $listings = $query->limit(10)->get();
		  ```
- # Test Queries Requests
	- ### Size in Memory
	- ### Time
	- ### Number of queries
	-
	-
	-
	-
	-