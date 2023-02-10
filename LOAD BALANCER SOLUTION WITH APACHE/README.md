# LOAD BALANCER SOLUTION WITH APACHE

After completing [the previous project](https://github.com/IwunzeGE/DevOps-Project/blob/main/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/README.md) you might wonder how a user will be accessing each of the webservers using 3 different IP addresses or 3 different DNS names. You might also wonder what is the point of having 3 different servers doing exactly the same thing.
When we access a website in the Internet we use an [URL](https://en.wikipedia.org/wiki/URL) and we do not really know how many servers are out there serving our requests, this complexity is hidden from a regular user, but in the case of websites that are being visited by millions of users per day (like Google or Reddit), it is impossible to serve all the users from a single Web Server (it is also applicable to databases, but for now we will not focus on distributed DBs).
Each URL contains a [domain name](https://en.wikipedia.org/wiki/Domain_name) part, which is translated (resolved) to the IP address of a target server that will serve requests when open a website in the Internet. Translation (resolution) of domain names is performed by [DNS servers](https://en.wikipedia.org/wiki/Domain_Name_Systemhttps://en.wikipedia.org/wiki/Domain_Name_System), the most commonly used one has a public IP address 8.8.8.8 and belongs to Google. You can try to query it with [nslookup](https://en.wikipedia.org/wiki/Nslookup) command:

`nslookup 8.8.8.8`
```Server:  UnKnown
Address:  103.86.99.99

Name:    dns.google
Address:  8.8.8.8
```
When you have just one Web server and load increases – you want to serve more and more customers, you can add more CPU and RAM or completely replace the server with a more powerful one – this is called "vertical scaling". This approach has limitations – at some point, you reach the maximum capacity of CPU and RAM that can be installed into your server.

Another approach used to cater for increased traffic is "horizontal scaling" – distributing the load across multiple Web servers. This approach is much more common and can be applied almost seamlessly and almost infinitely (you can imagine how many server Google has to serve billions of search requests).
Horizontal scaling allows adapting to the current load by adding (scale out) or removing (scale in) Web servers. Adjustment of a number of servers can be done manually or automatically (for example, based on some monitored metrics like CPU and Memory load).

The property of a system (in our case it is Web tier) to be able to handle the growing load by adding resources, is called ["Scalability"](https://en.wikipedia.org/wiki/Scalability).

In our set-up in [the previous project](https://github.com/IwunzeGE/DevOps-Project/blob/main/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/README.md), we had 3 Web Servers and each of them had its own public IP address and public DNS name. A client has to access them by using different URLs, which is not a nice user experience to remember the addresses/names of even 3 servers, let alone millions of [Google servers](https://en.wikipedia.org/wiki/Google_data_centers).

In order to hide all this complexity and to have a single point of access with a single public IP address/name, a [Load Balancer](https://en.wikipedia.org/wiki/Load_balancing_(computing)) can be used. A Load Balancer (LB) distributes clients’ requests among underlying Web Servers and makes sure that the load is distributed in an optimal way.

## Side Self-Study
Read about different Load Balancing concepts and differences between L4 Network LB and L7 Application LB.

Let us take a look at the updated solution architecture with an LB added on top of Web Servers (for simplicity let us assume it is a software L7 Application LB, for example – Apache, NGINX or HAProxy)

![a](https://github.com/IwunzeGE/DevOps-Project/blob/69fbf85618f227137e2148e5d5f9ca0f6210a0ba/LOAD%20BALANCER%20SOLUTION%20WITH%20APACHE/images/server%20diagram%200.png)

In this project, we will enhance our Tooling Website solution by adding a Load Balancer to distribute traffic between Web Servers and allow users to access our website using a single URL.

## Task
Deploy and configure an Apache Load Balancer for Tooling Website solution on a separate Ubuntu EC2 instance. Make sure that users can be served by Web servers through the Load Balancer.
To simplify, let us implement this solution with 2 Web Servers, the approach will be the same for 3 and more Web Servers.

## Prerequisites
Make sure that you have the following servers installed and configured within [the previous project](https://github.com/IwunzeGE/DevOps-Project/blob/main/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/README.md):
1.	Two RHEL8 Web Servers
2.	One MySQL DB Server (based on Ubuntu 20.04)
3.	One RHEL8 NFS server

![a](https://github.com/IwunzeGE/DevOps-Project/blob/69fbf85618f227137e2148e5d5f9ca0f6210a0ba/LOAD%20BALANCER%20SOLUTION%20WITH%20APACHE/images/server%20diagram%201.png)

## CONFIGURE APACHE AS A LOAD BALANCER

1.	Create an Ubuntu Server 20.04 EC2 instance and name it apache-lb.
 
2.	Open TCP port 80 on Project-8-apache-lb by creating an Inbound Rule in the Security Group.

3.	Install Apache Load Balancer on apache-lb server and configure it to point traffic coming to LB to both Web Servers:
#Install apache2

`sudo apt update` 
`sudo apt install apache2 -y` 
`sudo apt-get install libxml2-dev -y`

#Enable the following modules:
```sudo a2enmod rewrite
sudo a2enmod proxy
sudo a2enmod proxy_balancer
sudo a2enmod proxy_http
sudo a2enmod headers
sudo a2enmod lbmethod_bytraffic
```

#Restart apache2 service
`sudo systemctl restart apache2`

Make sure apache2 is up and running
`sudo systemctl status apache2`

Configure load balancing

`sudo nano /etc/apache2/sites-available/000-default.conf`

#Add this configuration into this section <VirtualHost *:80>  </VirtualHost>

```<Proxy "balancer://mycluster">
               BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
               BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
               ProxySet lbmethod=bytraffic
               # ProxySet lbmethod=byrequests
        </Proxy>

        ProxyPreserveHost On
        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/
```
![a](https://github.com/IwunzeGE/DevOps-Project/blob/ed5fc4ac756df1328ac5dc81612ff95c40c7c8a4/LOAD%20BALANCER%20SOLUTION%20WITH%20APACHE/images/nano.png)

#Restart apache server 
`sudo systemctl restart apache2`

[bytraffic](https://httpd.apache.org/docs/2.4/mod/mod_lbmethod_bytraffic.html) balancing method will distribute the incoming load between your Web Servers according to the current traffic load. We can control in which proportion the traffic must be distributed by loadfactor parameter.
You can also study and try other methods, like [bybusyness](https://httpd.apache.org/docs/2.4/mod/mod_lbmethod_bybusyness.html), [byrequests](https://httpd.apache.org/docs/2.4/mod/mod_lbmethod_byrequests.html), [heartbeat](https://httpd.apache.org/docs/2.4/mod/mod_lbmethod_heartbeat.html)

4.	Verify that our configuration works – try to access your LB’s public IP address or Public DNS name from your browser:
`http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php`
![a](https://github.com/IwunzeGE/DevOps-Project/blob/ed5fc4ac756df1328ac5dc81612ff95c40c7c8a4/LOAD%20BALANCER%20SOLUTION%20WITH%20APACHE/images/page.png)
**Note:** If in [the previous project](https://github.com/IwunzeGE/DevOps-Project/blob/main/DEVOPS%20TOOLING%20WEBSITE%20SOLUTION/README.md) you mounted /var/log/httpd/ from your Web Servers to the NFS server – unmount them and make sure that each Web Server has its own log directory.
`sudo umount -f /var/log/httpd`

Open two ssh/Putty consoles for both Web Servers and run the following command:
`sudo tail -f /var/log/httpd/access_log`
![a](https://github.com/IwunzeGE/DevOps-Project/blob/ed5fc4ac756df1328ac5dc81612ff95c40c7c8a4/LOAD%20BALANCER%20SOLUTION%20WITH%20APACHE/images/logs.png)

Try to refresh your browser page `http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php` several times and ensure both servers receive HTTP GET requests from your LB – new records must appear in each server’s log file. The number of requests to each server will be approximately the same since we set loadfactor to the same value for both servers – it means that traffic will be distributed evenly between them.

**OBSERVE THE NEW POST AND GET REQUESTS AFTER I REFREDHED THE PAGE FROM MY BROWSER**

![a](https://github.com/IwunzeGE/DevOps-Project/blob/ed5fc4ac756df1328ac5dc81612ff95c40c7c8a4/LOAD%20BALANCER%20SOLUTION%20WITH%20APACHE/images/logs%202.png)

If you have configured everything correctly – your users will not even notice that their requests are served by more than one server.

## Side Self-Study
Read more about different configuration aspects of [Apache mod_proxy_balancer module](https://httpd.apache.org/docs/2.4/mod/mod_proxy_balancer.html). Understand what sticky session means and when it is used.

## Optional Step – Configure Local DNS Names Resolution
Sometimes it is tedious to remember and switch between IP addresses, especially if you have a lot of servers under your management.
What we can do, is to configure local domain name resolution. The easiest way is to use /etc/hosts file, although this approach is not very scalable, it is very easy to configure and shows the concept well. So let us configure the IP address to domain name mapping for our LB.

#Open this file on your LB server

`sudo nano /etc/hosts`

#Add 2 records into this file with the Local IP address and arbitrary name for both of your Web Servers

```<WebServer1-Private-IP-Address> Web1
<WebServer2-Private-IP-Address> Web2
```
![a](https://github.com/IwunzeGE/DevOps-Project/blob/ed5fc4ac756df1328ac5dc81612ff95c40c7c8a4/LOAD%20BALANCER%20SOLUTION%20WITH%20APACHE/images/etc%20hostd.png)

Now you can update your LB config file with those names instead of IP addresses.

```BalancerMember http://Web1:80 loadfactor=5 timeout=1
BalancerMember http://Web2:80 loadfactor=5 timeout=1
```

You can try to curl your Web Servers from LB locally curl http://Web1 or curl http://Web2 – it shall work.
Remember, this is only an internal configuration and it is also local to your LB server, these names will neither be ‘resolvable’ from other servers internally nor from the Internet.
![a](https://github.com/IwunzeGE/DevOps-Project/blob/ed5fc4ac756df1328ac5dc81612ff95c40c7c8a4/LOAD%20BALANCER%20SOLUTION%20WITH%20APACHE/images/curl.png)

## Target Architecture
Now your set-up looks like this:

![a](https://github.com/IwunzeGE/DevOps-Project/blob/ed5fc4ac756df1328ac5dc81612ff95c40c7c8a4/LOAD%20BALANCER%20SOLUTION%20WITH%20APACHE/images/server%20diagram%202.png)

**Congratulations! You have just implemented a Load balancing Web Solution for your DevOps team.**
