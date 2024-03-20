---
title: Netbox Installation
description: 
published: 1
date: 2024-03-20T17:09:55.139Z
tags: 
editor: markdown
dateCreated: 2024-03-20T17:09:55.139Z
---

# Netbox Installation

> [NetBox](https://github.com/netbox-community/netbox) provides a cohesive, extensive, and accessible data model for all things networked. By providing a single robust user interface and programmable APIs for everything from cable maps to device configurations, NetBox serves as the central source of truth for the modern network.

#### Prerequisites

- a server/LXC running Ubuntu 22.04
- a fully qualified domain name (**FQDN**), such as *netbox.example.com*

<details id="bkmrk-step-1%3A-configure-fi"><summary>Step 1: Configure Firewall</summary>

<p class="callout info">First, configure the firewall. Ubuntu ships with ufw (Uncomplicated Firewall) by default.</p>

- [ ] Check if the firewall is running. 
  ```shell
    sudo ufw status
    ```
- [ ] Allow SSH port so the firewall doesn't break the current connection. 
  ```shell
    sudo ufw allow Open SSH
    ```
- [ ] Allow HTTP and HTTPS ports as well. 
  ```shell
    sudo ufw allow http
    sudo ufw allow https
    ```
- [ ] Enable the firewall and check the status. 
  ```shell
    sudo ufw enable && sudo ufw status
    ```

<p class="callout success">You should see an output similar to the following.  
</p>

```
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
80/tcp                     ALLOW       Anywhere                  
443                        ALLOW       Anywhere                  
8000                       ALLOW       Anywhere                  
8001                       ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
80/tcp (v6)                ALLOW       Anywhere (v6)             
443 (v6)                   ALLOW       Anywhere (v6)             
8000 (v6)                  ALLOW       Anywhere (v6)             
8001 (v6)                  ALLOW       Anywhere (v6)   
```

</details><details id="bkmrk-step-2%3A-install-and-"><summary>Step 2: Install and Configure PostgreSQL</summary>

---

---

- [ ] Install PostgreSQL. 
  ```shell
    sudo apt-get install postgresql postgresql-contrib
    ```
- [ ] Launch the PostgreSQL shell. After this command, you should be in the Postgres shell (`<em>postgres=#</em>`). 
  ```shell
    sudo -i -u postgres psql
    ```

---

- [ ] Create the database. 
  ```postgresql
    CREATE DATABASE netbox;
    ```
- [ ] Create the Netbox user and choose a strong password. 
  ```postgresql
    CREATE USER netbox WITH PASSWORD 'Your_Secure_Password';
    ```
- [ ] Change the database owner to the Netbox user. 
  ```postgresql
    ALTER DATABASE netbox OWNER TO netbox;
    ```
- [ ] The following steps are needed on Postgres 15 and later: 
  ```postgresql
    \connect netbox;
    ```
    
    ```postgresql
    GRANT CREATE ON SCHEMA public TO netbox;
    ```

<p class="callout info">Use `\q` to exit the PostgreSQL shell.  
</p>

---

- [ ] **Verify the service status.** We will be executing the `psql` command and passing the configured username/password. Be sure to replace `localhost` with your IP address, if using a remote database.

```
$ psql --username netbox --password --host localhost netbox
Password for user netbox: 
psql (12.5 (Ubuntu 12.5-0ubuntu0.20.04.1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
Type "help" for help.

netbox=> \conninfo
You are connected to database "netbox" as user "netbox" on host "localhost" (address "127.0.0.1") at port "5432".
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
netbox=> \q
```

<p class="callout success">If successful, you will enter a netbox prompt.   
  
Type `\conninfo` to confirm the connection, or type `\q` to exit the shell.</p>

</details><details id="bkmrk-step-3%3A-install-and-"><summary>Step 3: Install and Optionally Configure Redis</summary>

- [ ] Install Redis. 
  ```shell
    sudo apt-get install -y redis-server
    ```
- [ ] Verify the version is at least `v4.0`. 
  ```shell
    redis-server -v
    ```

<p class="callout info">You may wish to modify the Redis configuration at `/etc/redis.conf `or `/etc/redis/redis.conf`, but in most cases the default should be sufficient.</p>

- [ ] Verify the service status, using the redis-cli utility. 
  ```shell
    redis-cli ping
    ```

<p class="callout success">If succesful, you should receive a `PONG` response from Redis.</p>

</details><details id="bkmrk-step-4%3A-install-netb"><summary>Step 4: Download Netbox</summary>

<p class="callout info">We will be installing Netbox by `git` cloning the repository.</p>

- [ ] Install the prerequisites. 
  ```shell
    sudo apt install -y python3 python3-pip python3-venv python3-dev build-essential libxml2-dev libxslt1-dev libffi-dev libpq-dev libssl-dev zlib1g-dev git
    ```
- [ ] Verify the Python version is at least `3.8`. 
  ```shell
    python3 -V
    ```

---

- [ ] Make the base directory for Netbox. 
  ```shell
    sudo mkdir -p /opt/netbox/
    cd /opt/netbox/
    ```
- [ ] Clone the **master** branch of the GitHub repo in the current (base) directory. This branch always holds the current stable release. 
  ```shell
    sudo git clone -b master --depth 1 https://github.com/netbox-community/netbox.git .
    ```

<p class="callout info">The `git clone` command above utilizes a "shadow clone" to retrieve only the most recent commit. If you need to download the entire history, omit the `--depth 1` argument.  
  
**You should see output similar to the following:**</p>

```
Cloning into '.'...
remote: Enumerating objects: 996, done.
remote: Counting objects: 100% (996/996), done.
remote: Compressing objects: 100% (935/935), done.
remote: Total 996 (delta 148), reused 386 (delta 34), pack-reused 0
Receiving objects: 100% (996/996), 4.26 MiB | 9.81 MiB/s, done.
Resolving deltas: 100% (148/148), done.
```

---

- [ ] Create the Netbox System User, and assign ownership to necessary directories.  
    We'll also configure the WSGI and HTTP services to run under this account. 
  ```shell
    sudo adduser --system --group netbox
    sudo chown --recursive netbox /opt/netbox/netbox/media/
    sudo chown --recursive netbox /opt/netbox/netbox/reports/
    sudo chown --recursive netbox /opt/netbox/netbox/scripts/
    ```

</details><details id="bkmrk-step-5%3A-configure-ne"><summary>Step 5: Configure Netbox</summary>

- [ ] Switch to the Netbox configuration directory. 
  ```shell
    cd /opt/netbox/netbox/netbox/
    ```
- [ ] Copy the example configuration file to create the actual file. 
  ```shell
    sudo cp configuration_example.py configuration.py
    ```
- [ ] Generate the secret key and make a note of it in a safe place. 
  ```shell
    python3 ../generate_secret_key.py
    ```
    
    ---

Open the configuration file and edit the following (I included snippets of the corresponding sections):


  ```shell
sudo nano configuration.py
```

- [ ] `ALLOWED_HOSTS` | This is the list of valid hostnames and IP addresses by which the server can be reached. 
  ```python
    ALLOWED_HOSTS = ['netbox.example.com', 'Your_Server_IP', 'etc']
    ```
- [ ] `DATABASE` | This houses the database configuration. 
  ```python
    DATABASE = {
        'NAME': 'netbox',               # Database name
        'USER': 'netbox',               # PostgreSQL username
        'PASSWORD': 'J5brHrAXFLQSif0K', # PostgreSQL password
        'HOST': 'localhost',            # Database server
        'PORT': '',                     # Database port (leave blank for default)
        'CONN_MAX_AGE': 300,            # Max database connection age (seconds)
    }
    ```
- [ ] `REDIS` | This houses the Redis configuration; not much to change here. 
  ```python
    REDIS = {
        'tasks': {
            'HOST': 'localhost',      # Redis server
            'PORT': 6379,             # Redis port
            'PASSWORD': '',           # Redis password (optional)
            'DATABASE': 0,            # Database ID
            'SSL': False,             # Use SSL (optional)
        },
        'caching': {
            'HOST': 'localhost',
            'PORT': 6379,
            'PASSWORD': '',
            'DATABASE': 1,            # Unique ID for second database
            'SSL': False,
        }
    }
    ```
- [ ] `SECRET_KEY` | This will be the value we generated \[***and made a note of, right???***\] earlier. 
  ```python
    SECRET_KEY = 'Your_Suuper_Secure_Generated_Key'
    ```

</details><details id="bkmrk-step-6%3A-install-netb"><summary>Step 6: Install Netbox (Finally!!)</summary>

- [ ] Run the Netbox upgrade script. It'll take awhile. 
  ```shell
    sudo /opt/netbox/upgrade.sh
    
  ```
  
    
> This script performs the following:  
> - Creates a Python virtual environment  
> - Installs all required Python packages  
> - Run database schema migrations  
> - Builds local documentation (for offline use)  
> - Aggregate static resource files on the disk
{.is-info}


---

- [ ] Activate the virtual environment created by the script. 
  ```shell
    source /opt/netbox/venv/bin/activate
    ```
- [ ] Switch to the required directory and create the superuser to access Netbox. 
> The following steps will take place **inside** the virtual environment. In the shell, it will be denoted with `(venv) $` at the beginning of each line. I have excluded it to allow easy copy/paste functionality. I will make a note when the shell has been exited.
{.is-warning}

    
    
  ```shell
    cd /opt/netbox/netbox/
    python3 manage.py createsuperuser
    
  ```
    
You should receive output similar to the following: 
  ```
    Username (leave blank to use 'root'):
    Email address: netbox@example.com
    Password:
    Password (again):
    Superuser created successfully.
    
  ```
  
- [ ] Set up the housekeeping worker. 
  ```shell
    sudo ln -s /opt/netbox/contrib/netbox-housekeeping.sh /etc/cron.daily/netbox-housekeeping
    ```
- [ ] Open the necessary port to test the development instance, and test it! 
  ```shell
    sudo ufw allow 8000
    ```
    
    ```shell
    python3 manage.py runserver 0.0.0.0:8000 --insecure
    ```
    
> ***NOT FOR PRODUCTION USE***
> This development server is for development and testing purposes only. It is neither permanent not secure enough for production use.  
> **Do not use it in production.**
{.is-danger}

>     
> If successful, we should see output similar to the following (run `CONTROL-C` to exit):
{.is-info}

    
    
  ```
    Performing system checks...
    
    System check identified no issues (0 silenced).
    December 30, 2022 - 18:02:23
    Django version 4.1.4, using settings 'netbox.settings'
    Starting development server at http://127.0.0.1:8000/
    Quit the server with CONTROL-C.
    
  ```
    
> You should now be able to access your instance at the URL `http://<Your_IP>:8000/`.  
{.is-success}

Try logging in with the superuser we created earlier to verify it worked.

> You can exit the virtual environment shell (`exit`). The following steps are run as-is.  
>   
> If the test service does not run, or you cannot reach the Netbox homepage, **something has gone wrong. Do not proceed** with the rest of this guide until the installation has been corrected.
{.is-warning}


</details><details id="bkmrk-%C2%A0-1"><summary>Step 7: Configure Gunicorn and Create Service File</summary>

> Netbox runs as a WSGI app behind an HTTP server. Netbox automatically installs the Gunicorn server.   
> We will configure and create a service file for it. This allows it to run in the background and persist across reboots.
{.is-info}


- [ ] Create a copy of the Gunicorn configuration. 
  ```shell
    sudo cp /opt/netbox/contrib/gunicorn.py /opt/netbox/gunicorn.py
    ```
- [ ] Copy both Netbox and Gunicorn service files to the `/etc/systemd/system` directory. 
  ```shell
    sudo cp -v /opt/netbox/contrib/*.service /etc/systemd/system/
    ```
- [ ] Reload the service daemon. 
  ```shell
    sudo systemctl daemon-reload
    ```
- [ ] Start and Enable the `netbox` and `netbox-rq` services. 
  ```shell
    sudo systemctl start netbox netbox-rq
    sudo systemctl enable netbox netbox-rq
    ```
- [ ] Check the status of the WSGI service. 
  ```shell
    sudo systemctl status netbox
    ```

> You should see output similar to the following:
{.is-info}


```
● netbox.service - NetBox WSGI Service
     Loaded: loaded (/etc/systemd/system/netbox.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2021-08-30 04:02:36 UTC; 14h ago
       Docs: https://docs.netbox.dev/
   Main PID: 1140492 (gunicorn)
      Tasks: 19 (limit: 4683)
     Memory: 666.2M
     CGroup: /system.slice/netbox.service
             ├─1140492 /opt/netbox/venv/bin/python3 /opt/netbox/venv/bin/gunicorn --pid /va>
             ├─1140513 /opt/netbox/venv/bin/python3 /opt/netbox/venv/bin/gunicorn --pid /va>
             ├─1140514 /opt/netbox/venv/bin/python3 /opt/netbox/venv/bin/gunicorn --pid /va>
...
```

> If the NetBox service fails to start, issue the command `journalctl -eu netbox` to check for log messages that may indicate the problem.
{.is-warning}


</details>