## Hide domain from search engins
	- ### Prevent Crawling
		- create robots.txt 
		  
		  ```
		  User-agent: *
		  Disallow: /
		  
		  ```
	- ### Blocking indexing
		- Meta tag in html
		  
		  ```htmlembedded
		  <head>
		    <meta name="robots" content="noindex, nofollow">
		    <!-- Other head elements -->
		  </head>
		  ```
	- ### Password Protection
		- Lock domain with username and password
			- using cPanel
			  collapsed:: true
				- From Directory Privacy
				  collapsed:: true
					- choose root directory
					- create user name
					- create password
			-
		- Using .htpasswd 
		  
		  ```shell
		  htpasswd -c /path/to/.htpasswd username
		  ```
			- /path/to/.htpasswd :- path to place out of root directory
			- username :- create user name