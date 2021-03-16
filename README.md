# DeployDjango


 ### Install libs


  ```
  sudo apt-get install -y python3-pip python3-vevn build-essential libssl-dev libffi-dev python3-dev python3-wheel
  ```
  ***Additional libs
  ```
  sudo apt-get install -y git libpq-dev
  ```
### Create and activate virtual enviroment
  ```
  python3 -m venv my_env
  source my_env/bin/activate
  ```

###  Clone repository from github.com
  ```
  git clone ...
  ```

### Set PostgreSQL 
  Installation
  ```
  sudo apt install postgresql postgresql-contrib
  postgres service start
  sudo -u postgres psql
  ```
  Set postgres for Django
  ```
  ALTER ROLE myprojectuser SET client_encoding TO 'utf8';
  ALTER ROLE myprojectuser SET default_transaction_isolation TO 'read committed';
  ALTER ROLE myprojectuser SET timezone TO 'UTC';

  ```
  
  Create role
  ```
  sudo -u postgres psql
  CREATE DATABASE yourdbname;
  CREATE USER youruser WITH ENCRYPTED PASSWORD 'yourpass';
  GRANT ALL PRIVILEGES ON DATABASE yourdbname TO youruser;
  ```
  
  ### Configure PostgreSQL to allow remote connection
  
  By default PostgreSQL is configured to be bound to "localhost".
  To view all open ports we should execute ```ss -nlt``` on command line.
  We recieve an output like this:
  ```
  Proto Recv-Q Send-Q Local Address           Foreign Address         State
  tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN
  tcp        0      0 127.0.0.1:11211         0.0.0.0:*               LISTEN
  tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN
  tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
  tcp        0      0 127.0.0.1:5432          0.0.0.0:*               LISTEN
  tcp        0      0 127.0.0.1:3737          0.0.0.0:*               LISTEN
  ```
  As we can see above port 5432 is bound to 127.0.0.1. It means any attempt to connect to the postgresql server from outside the machine will be refused.
  In order to fix this issue we need to find ```postgresql.conf```. 
  ```
  sudo find / -name "postgresql.conf" | head -1 |sudo xargs -o vim
  ```
    
  Replace line
  ```
  listen_addresses = 'localhost'
  ```
  with
  ```
  listen_addresses = '*'
  ```
    
  Now restart postgresql server.
  ```
  sudo service postgresql restart
  ```
 


Some guides:
* [deploy django](https://habr.com/ru/post/501414/)
* [deploy django digitaloceon](https://www.digitalocean.com/community/tutorials/how-to-set-up-django-with-postgres-nginx-and-gunicorn-on-ubuntu-20-04-ru)
* [export/import postgres db](https://coderwall.com/p/acsbpw/heroku-export-postgres-database)

