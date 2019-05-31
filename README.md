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
***

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
    ErrorLog /var/log/httpd/mysite-error.log
</VirtualHost>
```
Uncomment and fill this line:
```ServerName myexampledomain.com:80```
```
sudo ./certbot-auto --debug
```
Edit *** /etc/httpd/conf.d/ssl.conf***
```
SSLCertificateFile /etc/letsencrypt/live/myexampledomain.com/fullchain.pem
SSLCertificateKeyFile /etc/letsencrypt/live/myexampledomain.com/privkey.pem
```
Yummy! Server with SSL is on!
***

### SQL & phpMyAdmin
```
sudo service mysqld start
sudo mysql_secure_installation
```
```
sudo chkconfig mysqld on
```
```
sudo yum install php70-mbstring.x86_64 php70-zip.x86_64 -y

sudo service httpd restart

cd /var/www/html
```
Head to [phpMyAdmin website](https://www.phpmyadmin.net/downloads/) and get latest tar version
```
wget https://files.phpmyadmin.net/phpMyAdmin/4.8.5/phpMyAdmin-4.8.5-all-languages.tar.gz

mkdir phpMyAdmin && tar -xvzf phpMyAdmin-4.8.5-all-languages.tar.gz -C phpMyAdmin --strip-components 1

rm phpMyAdmin-4.8.5-all-languages.tar.gz
```
Skkkrttt we got phpMyAdmin running!
***

### Node
```
sudo yum install -y gcc-c++ make

curl -sL https://rpm.nodesource.com/setup_6.x | sudo -E bash -

sudo yum install nodejs
```
Check if ok
```
node-v
npm -v
```

#### Node-red
```
sudo npm install -g node-red
```
**Run** `node-red` **once**, creating enviroment files.

```
sudo su
mkdir /home/ec2-user/.node-red/nodecerts
cp /etc/letsencrypt/live/myexampledomain.com/privkey.pem /home/ec2-user/.node-red/nodecerts/node-key.pem
cp /etc/letsencrypt/live/myexampledomain.com/privkey.pem /home/ec2-user/.node-red/nodecerts/node-cert.pem
```

Edit **/home/ec2-user/.node-red/settings.js** to enable ssl
At the beginning
```
var fs = require("fs");
```
Uncomment and link certs
```
https: {
    key: fs.readFileSync(''),
    cert: fs.readFileSync('/etc/letsencrypt/live/myexampledomain.com/fullchain.pem')
},
```
