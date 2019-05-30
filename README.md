## Blank server setup guide
##### Tested on the 2018/03 AMI release.
###### The purpose is to have all infos and commands in a single place, preventing errors and time losses.
I will report here a sequence of mainly not explained commands, these commands are gathered from [this](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/install-LAMP.html) and [this](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/SSL-on-amazon-linux-ami.html) AWS guides, [this node guide](https://www.e2enetworks.com/help/knowledge-base/how-to-install-node-js-and-npm-on-centos/) and personal issue fixes of the aforementioned. Head there if needed to understand what the commands actually do.
***


### Apache
```
sudo yum update -y
sudo yum install -y httpd24 php70 mysql56-server php70-mysqlnd
sudo service httpd start
```
Start at each boot:
```
sudo chkconfig httpd on
```
Permissions:
```
sudo usermod -a -G apache ec2-user

exit
```
```
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www
find /var/www -type d -exec sudo chmod 2775 {} \;
find /var/www -type f -exec sudo chmod 0664 {} \;
```


### Letsencrypt
~~`sudo yum install mod24_ssl`~~
```
sudo yum install mod24_ssl.x86_64

sudo yum-config-manager --enable epel
wget https://dl.eff.org/certbot-auto
chmod a+x certbot-auto
```
Edit **/etc/httpd/conf/httpd.conf** to let vhost listen to port 80
Add this at end
```
<VirtualHost *:80>
    ServerName myexampledomain.com
    ServerAlias myexampledomain.com
    ErrorLog ${APACHE_LOG_DIR}/mysite-error.log
    CustomLog ${APACHE_LOG_DIR}/mysite-access.log
</VirtualHost>
```
```
sudo ./certbot-auto --debug
```
