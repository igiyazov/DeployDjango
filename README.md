# DeployDjango

### 

#### Install libs
---
  ```
  sudo apt-get install -y python3-pip python3-vevn build-essential libssl-dev libffi-dev python3-dev git
  ```

* #### Create and activate virtual enviroment
  ```
  python3 -m venv my_env
  source my_env/bin/activate
  ```

* ####  Clone repository from github.com
  ```
  git clone ...
  ```

* #### Set PostgreSQL 
  Installation
  ```
  sudo apt install postgresql postgresql-contrib
  ```
  
  Create role
  ```
  sudo -u postgres psql
  CREATE DATABASE yourdbname;
  CREATE USER youruser WITH ENCRYPTED PASSWORD 'yourpass';
  GRANT ALL PRIVILEGES ON DATABASE yourdbname TO youruser;
  ```
  

