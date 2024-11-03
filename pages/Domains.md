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
	-