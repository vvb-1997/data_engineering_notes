Run Below Commands in the VM Instance

```bash
#! /bin/bash
sudo su
apt update
apt -y install apache2
sudo service apache2 start
sudo update-rc.d apache2 enable
echo "Hello World" > /var/www/html/index.html
echo "Hello world from $(hostname) $(hostname -I)" > /var/www/html/index.html
```

Commands:
- `sudo su` - execute commands as a root user
- `apt update` - Update package index - pull the latest changes from the APT repositories
- `apt -y install apache2` - Install apache 2 web server
- `sudo service apache2 start` - Start apache 2 web server
- `echo "Hello World"` > /var/www/html/index.html - Write to index.html
- `$(hostname)` - Get host name
- `$(hostname -I)` - Get host internal IP address