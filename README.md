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
