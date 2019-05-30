### Blank server setup guide
##### Tested on the 2018/03 AMI release.
###### The purpose is to have all infos and commands in a single place, preventing errors and time losses.
I will report here a sequence of mainly not explained commands, these commands are gathered from [this AWS guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/install-LAMP.html), [this node guide](https://www.e2enetworks.com/help/knowledge-base/how-to-install-node-js-and-npm-on-centos/), and [this Certbot guide](https://certbot.eff.org/lets-encrypt/centos6-apache). Head there if needed to understand what the commands actually do.
***

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



```
sudo usermod -a -G apache ec2-user
```
