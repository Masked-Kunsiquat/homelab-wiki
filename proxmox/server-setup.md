---
title: New Server Setup
description: 
published: 1
date: 2024-03-20T16:44:28.805Z
tags: 
editor: markdown
dateCreated: 2024-03-20T16:44:28.805Z
---

# New Server Setup

<p class="callout info">I'm going to use this page to list out the steps I typically take to set up a generic server.   
I will eventually use this guide as an outline to build an Ansible role.</p>

<details id="bkmrk-create-the-user-crea"><summary>Create the User</summary>

- Create a new user ("severadmin"). ```shell
    adduser serveradmin
    ```
- Add the new user to the sudo group ```
    usermod -aG sudo serveradmin
    ```

</details><details data-id="details-1709596535352" id="bkmrk-configure-ssh-settin"><summary>Configure SSH Settings</summary>

### **Remote Host**

##### Configure `sshd_config` file

1. Edit the ssh config file to allow ssh  
    ```shell
    sudo nano /etc/ssh/sshd_config
    ```
2. Configure the following components:  
      
    - `AuthorizedKeysFile` | uncomment or add this line; this will direct the ssh-agent to `./ssh/authorized_keys`  
    - `PasswordAuthentication` | uncomment this line, and ensure it is set to "`no`"  
    - `PermitRootLogin` | uncomment this line, and set it to "`no`"; disables root ssh-access  
    - `UsePAM` | ensure this is uncommented and set to "`yes`"

##### Configure `authorized_keys` file

1. Ensure that you're in the directory of the desired user (`serveradmin` in this example).   
    ```shell
    2arry:~$ pwd
    /home/serveradmin
    ```
2. Add an SSH directory, if it doesn't exist.  
    ```shell
    sudo mkdir .ssh
    ```
3. Change into the new directory and add a new file, `authorized_keys.`  
    ```shell
    cd .ssh && touch authorized_keys
    ```
4. Edit the `authorized_keys` file and paste in your desired SSH **public-**key.  
    ```shell
    sudo nano authorized_keys
    ```
    
    <p class="callout info">Your file will look similar to below. Each key will have its own line in the `authorized_keys` file.</p>
    
    ```
      GNU nano 7.2                       authorized_keys                   Modified  
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDV2qoTZ+9/t31andY0ZS7TNlcu1j0nJXbuUJ2jYZH>
    
    
    ^G Help      ^O Write Out ^W Where Is  ^K Cut       ^T Execute   ^C Location
    ^X Exit      ^R Read File ^\ Replace   ^U Paste     ^J Justify   ^/ Go To Line
    ```
5. Fix the permissions of the `authorized_keys` file.  
    ```shell
    sudo chmod 600 ~/.ssh/authorized_keys
    ```
6. Restart the `sshd` service. ```shell
    sudo systemctl restart sshd
    ```

### **Orchestrator:**

```shell
ssh-copy-id -i serveradmin@[REMOTE_HOST]
```

#####  Configure the `.ssh/config` File

<p class="callout info">In the example below, using the command "`ssh debian`" will now be the equivalent of   
"`ssh serveradmin@10.0.0.10`."   
</p>

```
# ~/.ssh/config

Host [DESIRED_HOSTNAME]
HostName [IP_ADDRESS]
User [USERNAME]
IdentityFile [PRIVATE_KEY_PATH]

Host debian
HostName 10.0.0.10
User serveradmin
IdentityFile ~/.ssh/keys/id_rsa_debian
```

</details><details id="bkmrk-generic-commands-lin"><summary>Generic Commands</summary>

##### Linux User Modifications

- Create a new user (`USERNAME`) ```
    adduser [USERNAME]
    ```
- Add a user to a group (`GROUP_NAME`) ```
    usermod -aG [GROUP_NAME] [USERNAME]
    ```

##### SSH Configurations

- Forward SSH-key (`ssh-pub-file`) to remote host from orchestrator ```
    ssh-copy-id -i <ssh-pub-file> [USERNAME]@[REMOTE_HOST]
    ```
- Copy an SSH public-key (`ssh-pub-key`) on the remote host to the `authorized_keys` file  
    ```shell
    cd             # Navigate to the user's home directory
    cd .ssh        # Navigate to the .ssh directory
    
    # If the .ssh directory doesn't exist, create it
    mkdir -p ~/.ssh
    
    # Append the public key to the authorized_keys file
    echo "<ssh-pub-key>" >> authorized_keys
    
    # Make sure permissions are set correctly
    chmod 600 authorized_keys
    ```

</details>
