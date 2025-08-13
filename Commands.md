

**Network**

tcpdump -i eth0 port 80


**Add swap**

sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

Increase container or VM memory limits : `Kubernetes/Docker: adjust resources.limits.memory`



**Database**

1. Check slow-query log (MySQL) `sudo tail -n 50 /var/log/mysql/mysql-slow.log`


**Memory**
