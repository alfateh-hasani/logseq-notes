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
- ## Basic git commands
	- ### git status of files
	  
	  ```shell
	  git status
	  ```
	- ### track file (Stagin)
	  
	  ```shell
	  git add [file path/filename]
	  ```
	- ### track all files (Staging)
	  
	  ```shell
	  git all .
	  ```
	- ### untrack file
	  
	  ```shell
	  git rm --cached [file path/filename]
	  ```
	- ### remove from staging
	  
	  ```shell
	  git restore --staged [file path/ filename]
	  ```
	- ### remove changes in file, recover deleted file
	  ```shell
	  git restore [file path/filename]
	  ```
	- ### ignore file create file .gitignore
	  both ways are correct
	  
	  ```shell
	  # ignore all .txt files
	  *.txt
	  ```
	- ### to commit all tracked files
	  
	  ```shell
	  git commit -m "[message for all files]"
	  ```
	- ### Review the changes 
	  
	  ```shell
	  git diff
	  ```
	- ### Stage and commit 
	  
	  ```shell
	  git commit -a -m "[message for all files]"
	  ```
	- ### Review Log , blog as one line
	  
	  ```git log
	  git log
	  git log --oneline
	  ```
	- ### Change message of last commit
	  
	  ```shell
	  git commit -m "[New message]" --amend
	  ```
	- ### Return to previous commit 
	  
	  ```shell
	  git reset [hash code of commit from log]
	  ```
	- ### Branches
		- Active branch
		  
		  ```shell
		  git branch
		  ```
		- create branch
		  
		  ```shell
		  git branch [name]
		  ```
		- switch between branches & create
		  
		  ```shell
		  git switch [name]
		  
		  # create and switch
		  git switch -c [name]
		  ```
		- Merge changes from branch to current branch
		  
		  ```shell
		  git merge -m "[Message of merge]"
		  ```
		- Merge if there is conflicts
		  
		  ```shell
		  // solve it manually then commit it
		  git commit -a -m "[Message of merge after solving confilect]"
		  ```
		- Delete branch
		  
		  ```shell
		  git branch -d [name]
		  ```
		-
		-
- ## Advaced commands
	- ### Rebase (Change order, merge, squash, ...) commits 
	  
	  ```shell
	  git rebase -i --root
	  ```
- ## general command
	- ### remove file
	  
	  ```shell
	  git rm "[file path/filename]"
	  ```
	- Move or rename file
	  
	  ```shell
	  git mv "[file path/filename old]" "[file path/filename new]"
	  ```
- ## Github
	- create new repository
	- connect new repository from local
		- ```shell
		  git init
		  git add README.md
		  git commit -m "first commit"
		  git branch -M main
		  git remote add origin "[URL]"
		  git push -u origin main
		  ```
	- connect an existing repository
		- ```shell
		  git remote add origin
		  ```