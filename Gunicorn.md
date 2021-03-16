# Gunicorn settings 
## Set up ```gunicorn.conf.py```
First we need to create gunicorn configure file ```gunicorn.conf.py```
Also you can name the file somehow, but gunicorn by default will be read a file named ```gunicorn.conf.py``` from the same directory where gunicorn is being run.
Exampl```gunicorn.conf.py```:
```python
command='/home/www/project/youcleve/v1/v1_env/bin/gunicorn'
pythonpath='/home/www/project/youcleve/v1/youcleve'
bind='unix:/run/gunicorn.sock'
workers=5
user='www'
```
Let's test gunicorn:
```
gunicorn --bind 0.0.0.0:8000 myproject.wsgi
```
If all ok, we can go on!

## Creating systemd Socket and Service Files for Gunicorn
We have tested that Gunicorn can interact with our Django application, but we should implement a more robust way of starting and stopping the application server. To accomplish this, we’ll make systemd service and socket files.
  
  
The Gunicorn socket will be created at boot and will listen for connections. When a connection occurs, systemd will automatically start the Gunicorn process to handle the connection.
  
  
Start by creating and opening a systemd socket file for Gunicorn with ```sudo``` privileges:
```
sudo vim /etc/systemd/system/gunicorn.socket
```
  
  
Inside, we will create a ```[Unit]``` section to describe the socket, a ```[Socket]``` section to define the socket location, and an ```[Install]``` section to make sure the socket is created at the right time:
```
[Unit]
Description=gunicorn socket

[Socket]
ListenStream=/run/gunicorn.sock

[Install]
WantedBy=sockets.target
```

Next, create and open a systemd service file for Gunicorn with sudo privileges in your text editor. The service filename should match the socket filename with the exception of the extension:
```
sudo nano /etc/systemd/system/gunicorn.service
```

```
[Unit]
Description=gunicorn daemon
Requires=gunicorn.socket
After=network.target

[Service]
User=sammy
Group=www-data
WorkingDirectory=/home/sammy/myprojectdir
ExecStart=/home/sammy/myprojectdir/myprojectenv/bin/gunicorn \
          --access-logfile - \
          myproject.wsgi:application

[Install]
WantedBy=multi-user.target
```

With that, our systemd service file is complete. Save and close it now.
  
We can now start and enable the Gunicorn socket. This will create the socket file at /run/gunicorn.sock now and at boot. When a connection is made to that socket, systemd will automatically start the gunicorn.service to handle it:

```
sudo systemctl enable gunicorn.socket  
sudo systemctl start gunicorn.socket
```

## Checking for the Gunicorn Socket File
Check the status of the process to find out whether it was able to start:
```
sudo systemctl status gunicorn
```
  
You should receive an output like this:
```
● gunicorn.socket - gunicorn socket
     Loaded: loaded (/etc/systemd/system/gunicorn.socket; enabled; vendor prese>
     Active: active (listening) since Fri 2020-06-26 17:53:10 UTC; 14s ago
   Triggers: ● gunicorn.service
     Listen: /run/gunicorn.sock (Stream)
      Tasks: 0 (limit: 1137)
     Memory: 0B
     CGroup: /system.slice/gunicorn.socket
```
  
Check the Gunicorn socket’s logs by typing:
```
sudo journalctl -u gunicorn.socket
```

If you make changes to the ```/etc/systemd/system/gunicorn.service``` file, reload the daemon to reread the service definition and restart the Gunicorn process by typing:
```
sudo systemctl daemon-reload
sudo systemctl restart gunicorn
```

To view all open ports
```ss -lntu```
  
  
## Useful links
- [Set Up Django with Postgres, Nginx, and Gunicorn manual by Digitalocean](https://www.digitalocean.com/community/tutorials/how-to-set-up-django-with-postgres-nginx-and-gunicorn-on-ubuntu-20-04)
- [Gunicorn config file settings](https://docs.gunicorn.org/en/stable/settings.html#config-file)
