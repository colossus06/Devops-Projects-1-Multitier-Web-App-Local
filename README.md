# Devops-Projects


![image](https://user-images.githubusercontent.com/96833570/211034798-89b29696-0ca5-4307-9f75-623a6d307cc1.png)

* Nginx VM: web01
* tomcat vm: app01
* RabbitMQ vm: rmq01
* Memcache vm: mc01
* DB vm: db01

```
vagrant ssh web01
cat /etc/hosts
ping app01
logout
```

```
vagrant ssh app01
cat /etc/hosts
ping mc01
ping rmq01
ping db01
logout
```
# BACKEND SETUP

## 1- DB setup

```
vagrant ssh db01
sudo -i
yum update -y
echo "DB_PSW='psw123'" >> /etc/profile
source /etc/profile
yum install epel-release -y
yum install git mariadb-server -y
sleep 10
systemctl start mariadb
systemctl enable mariadb
mysql_secure_installation
# Set the variable password here
mysql -u root -ppsw123
# or mysql -u root -p

mysql --version

git clone -b local-setup https://github.com/devopshydclub/vprofile-project.git
cd vprofile-project
mysql -u root -p"$DB_PSW" -e "create database accounts"
mysql -u root -p"$DB_PSW" -e "grant all privileges on accounts.* TO 'admin'@'app01' identified by 'psw123' "




mysql -u root -ppsw123 accounts < src/main/resources/db_backup.sql
mysql -u root -ppsw123 -e "FLUSH PRIVILEGES"
mysql -u root -p"$DB_PSW"
show databases;
use accounts;
show tables;



systemctl restart mariadb




```

![image](https://user-images.githubusercontent.com/96833570/211056824-cb151598-3fcf-4e2a-a9d5-b5abc98b5b74.png)

![image](https://user-images.githubusercontent.com/96833570/211056980-a86fa573-83c9-4d77-8b82-1c8b57cc944f.png)


## 2- Memcached setup

Install, start & enable memcache on port 11211

```
vagrant ssh mc01
sudo -i
yum update -y 
yum install epel-release -y
yum install memcached -y
systemctl start memcached 
systemctl enable memcached
systemctl status memcached
memcached -p 11211 -U 11111 -u memcached -d
```

![image](https://user-images.githubusercontent.com/96833570/211066706-59508adc-54ed-42f2-9912-e7c60e65a59e.png)



## 3- RabbitMQ setup

```
vagrant ssh rmq01
cat /etc/hosts
sudo -i
yum update -y
yum install epel-release -y
yum install wget -y
cd /tmp/
wget http://packages.erlang-solutions.com/erlang-solutions-2.0-1.noarch.rpm
sudo rpm -Uvh erlang-solutions-2.0-1.noarch.rpm
sudo yum -y install erlang socat

# Install rabbitmq

curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash
sudo yum install rabbitmq-server -y


# Start rabbitmq
systemctl start rabbitmq-server
systemctl enable rabbitmq-server
systemctl status rabbitmq-server

Config Change
sh -c 'echo "[{rabbit, [{loopback_users, []}]}]." > /etc/rabbitmq/rabbitmq.config'
rabbitmqctl add_user rabbit psw123
rabbitmqctl set_user_tags rabbit administrator


systemctl restart rabbitmq-server

```

# Debug

`ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)`

Solution: Add your password adjacent to the -p flag like: `-pmyuniquepass -p123456


# APP SETUP

## Tomcat Server Setup

```
vagrant ssh app01
cat /etc/hosts
sudo -i
yum update -y
yum install epel-release -y

yum install java-1.8.0-openjdk -y
yum install git maven wget -y

cd /tmp/
wget https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.37/bin/apache-tomcat-8.5.37.tar.gz
tar xzvf apache-tomcat-8.5.37.tar.gz

 useradd --home-dir /usr/local/tomcat8 --shell /sbin/nologin tomcat
 
cp -r /tmp/apache-tomcat-8.5.37/* /usr/local/tomcat8/
chown -R tomcat.tomcat /usr/local/tomcat8

nano  /etc/systemd/system/tomcat.service


```
