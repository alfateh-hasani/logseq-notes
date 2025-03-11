### Telescope provider with tags and authenticating using auth gaurd
	- ```php
	  <?php
	  namespace App\Providers;
	  
	  use Illuminate\Support\Facades\Gate;
	  use Illuminate\Support\Facades\Log;
	  use Laravel\Telescope\IncomingEntry;
	  use Laravel\Telescope\Telescope;
	  use Laravel\Telescope\TelescopeApplicationServiceProvider;
	  
	  class TelescopeServiceProvider extends TelescopeApplicationServiceProvider
	  {
	      /**
	       * Register any application services.
	       */
	      public function register(): void
	      {
	          // Only register Telescope if it's enabled
	          if (config('telescope.enabled')) {
	              Telescope::night();
	  
	              $this->hideSensitiveRequestDetails();
	  
	              // Check environment
	              $isLocal = $this->app->environment('local') || $this->app->environment('production');
	  
	              // Filter entries
	              Telescope::filter(function (IncomingEntry $entry) use ($isLocal) {
	                  // Always record request entries
	                  // if ($entry->type === 'request') {
	                  //     return true;
	                  // }
	  
	                  return $isLocal ||
	                      $entry->isReportableException() ||
	                      $entry->isFailedRequest() ||
	                      $entry->isFailedJob() ||
	                      $entry->isScheduledTask() ||
	                      $entry->hasMonitoredTag();
	              });
	  
	              // Tag entries
	              Telescope::tag(function (IncomingEntry $entry) {
	                  // Debug log to see what entries are being processed
	  
	                  if ($entry->type === 'request') {
	                      // Log::info('Telescope tagging entry type: ' . $entry->type);
	                      // Debug log to see the content structure
	                      // Log::info('Request entry content: ' . json_encode($entry->content));
	  
	                      $tags = [];
	  
	                      // Add status code tags
	                      if (isset($entry->content['response_status'])) {
	                          $status = $entry->content['response_status'];
	                          $tags[] = "status:{$status}";
	  
	                          // Add status category tags
	                          if ($status >= 200 && $status < 300) {
	                              $tags[] = 'status:success';
	                          } elseif ($status >= 300 && $status < 400) {
	                              $tags[] = 'status:redirect';
	                          } elseif ($status >= 400 && $status < 500) {
	                              $tags[] = 'status:client-error';
	                          } elseif ($status >= 500) {
	                              $tags[] = 'status:server-error';
	                          }
	                      }
	  
	                      // Add HTTP verb/method tags
	                      if (isset($entry->content['method'])) {
	                          $method = strtolower($entry->content['method']);
	                          $tags[] = "verb:{$method}";
	  
	                          // Group verbs by type
	                          if (in_array($method, ['get', 'head', 'options'])) {
	                              $tags[] = 'verb:read';
	                          } elseif (in_array($method, ['post', 'put', 'patch'])) {
	                              $tags[] = 'verb:write';
	                          } elseif ($method === 'delete') {
	                              $tags[] = 'verb:delete';
	                          }
	                      }
	  
	                      // Add duration tags
	                      if (isset($entry->content['duration'])) {
	                          $duration = $entry->content['duration'];
	  
	                          if ($duration < 100) {
	                              $tags[] = 'duration:fast';
	                          } elseif ($duration < 500) {
	                              $tags[] = 'duration:medium';
	                          } elseif ($duration < 1000) {
	                              $tags[] = 'duration:slow';
	                          } else {
	                              $tags[] = 'duration:very-slow';
	                          }
	  
	                          // Add specific duration range
	                          $durationRange = floor($duration / 100) * 100;
	                          $tags[] = "duration:{$durationRange}-" . ($durationRange + 100) . "ms";
	                      }
	  
	                      // Add path tags
	                      if (isset($entry->content['uri'])) {
	                          $uri = $entry->content['uri'];
	                          $path = parse_url($uri, PHP_URL_PATH);
	  
	                          // Tag by first segment
	                          $segments = explode('/', trim($path, '/'));
	                          if (!empty($segments[0])) {
	                              $tags[] = "path:{$segments[0]}";
	                          }
	  
	                          // Tag API routes
	                          if (str_starts_with($path, '/api')) {
	                              $tags[] = 'path:api';
	  
	                              // Tag API version if present
	                              if (isset($segments[1]) && preg_match('/^v\d+$/', $segments[1])) {
	                                  $tags[] = "api-version:{$segments[1]}";
	                              }
	                          }
	                      }
	  
	                      // Log the generated tags
	                      // Log::info('Generated tags: ' . json_encode($tags));
	  
	                      return $tags;
	                  }
	  
	                  return [];
	              });
	          }
	      }
	  
	      /**
	       * Prevent sensitive request details from being logged by Telescope.
	       */
	      protected function hideSensitiveRequestDetails(): void
	      {
	          if ($this->app->environment('local')) {
	              return;
	          }
	  
	          Telescope::hideRequestParameters(['_token']);
	  
	          Telescope::hideRequestHeaders([
	              'cookie',
	              'x-csrf-token',
	              'x-xsrf-token',
	          ]);
	      }
	  
	      /**
	       * Register the Telescope gate.
	       *
	       * This gate determines who can access Telescope in non-local environments.
	       */
	      protected function gate(): void
	      {
	          Gate::define('viewTelescope', function ($user = null) {
	              // If we're in local environment, allow access
	              if ($this->app->environment('local')) {
	                  return true;
	              }
	  
	              // Fix email addresses
	              $allowedEmails = [
	                  "alfateh@gmail.com",
	              ];
	  
	              // Check admin guard
	              if (auth()->guard('admin')->check()) {
	                  return in_array(auth()->guard('admin')->user()->email, $allowedEmails);
	              }
	  
	              // No valid authentication
	              return false;
	          });
	      }
	  }
	  
	  ```