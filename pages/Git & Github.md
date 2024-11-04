- install
- ## initial setup  for git
	- Config name & email
	  
	  ```shell
	  git config --global user.name "[user name]"
	  git config --global user.email "[email]"
	  ```
	- Config default branch name
	  
	  ```shell
	  git config --global init.default branch main
	  ```
	- Ask help for command F.X config
	  
	  ```shell
	  git config -h
	  ```
		- open manual help web
		  
		  ```shell
		  git help config
		  ```
- ## start in project path
	- create git folder 
	  
	  ```shell
	  git init	
	  ```
- ## git commands
	- ### git status of files
	  
	  ```shell
	  git status
	  ```
	- track file
	  
	  ```shell
	  git add [file path/filename]
	  ```
	- track all files
	  
	  ```shell
	  git all .
	  ```
	- untrack file
	  
	  ```shell
	  git rm --cached [file path/filename]
	  ```
	  or use 
	  
	  ```shell
	  git restore [file path/filename]
	  ```
	- ignore file create file .gitignore
	  both ways are correct
	  
	  ```shell
	  # ignore all .txt files
	  *.txt
	  ```
	- to commit all tracked files
	  
	  ```shell
	  git commit -m "[message for all files]"
	  ```
	-