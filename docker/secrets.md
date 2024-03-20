---
title: Secrets Management
description: 
published: 1
date: 2024-03-20T06:36:44.285Z
tags: 
editor: markdown
dateCreated: 2024-03-20T06:36:44.285Z
---

I've switched over to 1Password fully to handle my Secrets Management.
Until I find a simpler way, this is my attempt to having dynamic docker-compose files that can be version-controlled. 

To achieve this:
- I placed a ref.env file in each project directory.
- I edit my compose file to remove hardcoded variables and utilize placeholders (i.e. `${PLACEHOLDER}`).
- I run a command to generate an .env file and inject the secrets into it (from the ref.env file).
- Then I spin up my container and remove the .env file.
This allows me to version-control all my files (except the .env files).

I'm pretty lazy though, so I utilized ChatGPT to throw together an interactive script to simplify the process (see below).
Here's the script in its entirety:</div><div id="bkmrk-attachment%3A%22deploy.s"><details><summary>deploy.sh</summary>

```shell
#!/bin/bash

export OP_CONNECT_HOST=http://localhost:8080

read -s -p "Enter the value for OP_CONNECT_TOKEN: " OP_CONNECT_TOKEN
echo 


if [ -n "$OP_CONNECT_TOKEN" ]; then
    export OP_CONNECT_TOKEN

    echo "Injecting secrets into .env..."
    op inject -i ref.env -o .env

    if [ $? -eq 0 ]; then
        echo "Starting Docker Compose services..."
        docker-compose up -d

        if [ $? -eq 0 ]; then
            # Step 3: Remove .env file
            echo "Removing .env file..."
            rm .env
            echo "Script completed successfully."
        else
            echo "Error: Failed to start Docker Compose services."
        fi
    else
        echo "Error: Failed to inject secrets into .env."
    fi
else
    echo "Error: OP_CONNECT_TOKEN cannot be empty. Exiting."
fi
```
</details></div><div id="bkmrk-"></div><div id="bkmrk--1"></div><div id="bkmrk--2"></div>
---
# **Script Methodology**

Here's the script in its entirety: **{place_script_link_here}**

I then copied it to the `$PATH` for universal usage (run from wherever you saved the script):

    sudo cp deploy.sh /usr/local/bin/

## I'm going to use this page to break down the pieces for future reference.

 - [ ] Set the OP_CONNECT_HOST environment variable.
	 ```
	 export OP_CONNECT_HOST=http://localhost:8080
	 ```
 - [ ] Request user input for OP_CONNECT_TOKEN **(hidden).**
	```
	read -s -p "Enter the value for OP_CONNECT_TOKEN: " OP_CONNECT_TOKEN 
	echo # Add a newline after the hidden input
	```

 - [ ] Check if the entered token is not empty.
	```
	if [ -n "$OP_CONNECT_TOKEN" ]; then
	```

 - [ ] Inject secrets into .env file.
	```
	  	echo "Injecting secrets into .env..."
	    op inject -i ref.env -o .env
	```
 - [ ] Check if injection was successful.
	 ```
	  if [ $? -eq 0 ]; then
	          # Step 2: Start Docker Compose services
	          echo "Starting Docker Compose services..."
	          docker-compose up -d
	```

 - [ ] Check if Docker Compose was started successfully.
	```
	if [ $? -eq 0 ]; then
	            # Step 3: Remove .env file
	            echo "Removing .env file..."
	            rm .env
	            echo "Script completed successfully."
	        else
	            echo "Error: Failed to start Docker Compose services."
	        fi
	    else
	        echo "Error: Failed to inject secrets into .env."
	    fi
	else
	    echo "Error: OP_CONNECT_TOKEN cannot be empty. Exiting."
	fi
	```
