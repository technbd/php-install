
## HTTPD ProxyPass:

"ProxyPass" is a directive used in the Apache HTTP Server (httpd) configuration to set up a reverse proxy. It allows Apache to forward requests to another server or location based on certain conditions. This is commonly used for scenarios like load balancing, serving applications from different servers, or routing requests to backend services.

When configuring ProxyPass, you specify a URL path and the destination where requests matching that path should be forwarded. Additionally, you typically use ProxyPassReverse to ensure that any headers or URLs in the response from the backend server are modified to match the public URL.


Syntax of ProxyPass:

```
ProxyPass [path] [url]
ProxyPass / http://your_backend_server:3000

ProxyPass / http://localhost:3000
```


Syntax of ProxyPassReverse:

```
ProxyPassReverse [path] [url]
ProxyPassReverse / http://your_backend_server:3000

ProxyPassReverse / http://localhost:3000
```



Ensure that Apache HTTP Server is installed on your system. You can install it using your system's package manager. For example, on CentOS-7:

```
yum install httpd -y
```


```
systemctl start httpd
systemctl status httpd
```


```
grep "mod_proxy" /etc/httpd/conf.modules.d/00-proxy.conf
```


### Run Docker Containers: 
```
docker run --name web1 -dit -p 8181:80 nginx
docker run --name web2 -dit -p 8182:80 nginx
docker run --name web3 -dit -p 8183:80 nginx
```


### Configure ProxyPass Directives:

Edit the Apache configuration file, typically located at "/etc/httpd/conf/httpd.conf". Alternatively, you can create a new configuration file under "/etc/httpd/conf.d/" with a .conf extension (e.g., proxy.conf), and include it in the main configuration. 

- **"ProxyPass"** Specifies that all requests / are forwarded to the back-end application port. When a client sends a request to Apache, and Apache forwards that request to a backend server using ProxyPass, the backend server might generate responses with content or headers that contain URLs pointing back to itself. This could cause issues for clients since they expect resources to be served from the Apache server.

- **"ProxyPassReverse"** Negates ProxyPass by modifying response headers from the back-end application. ProxyPassReverse modifies the URLs in the response headers and HTML content of the proxied server's responses to match the URL of the reverse proxy. This ensures that clients receive correct URLs and can properly navigate the proxied content.

- When "**ProxyPreserveHost**" enabled, this option will pass the Host: line from the incoming request to the proxied host (backend server), instead of the hostname specified in the ProxyPass line. Forwards the original host header to the back-end application.

This option should normally be turned Off. It is mostly useful in special configurations like proxied mass name-based virtual hosting, where the original Host header needs to be evaluated by the backend server.

- The "**ProxyRequests**" directive in Apache HTTP Server is used to enable or disable proxy functionality. When ProxyRequests is set to On, Apache will act as a forward proxy, allowing clients to request resources through Apache to other servers. This is useful for scenarios where clients are restricted from directly accessing certain resources on the internet, such as in corporate networks where internet access is mediated through a proxy server.

Therefore, ProxyRequests should generally be set to Off unless you explicitly intend to use Apache as a forward proxy. It should typically be set to “off” when using ProxyPass to avoid misuse of the server as an open proxy and protect against potential security risks.

-  **"RequestHeader set Host"** directive in Apache HTTP Server (httpd) is used to modify the Host header of incoming HTTP requests. The Host header specifies the domain name of the server to which the request is being sent and is crucial for virtual hosting, reverse proxying, and routing requests to the correct destination. That directive allows you to control and manipulate the Host header of incoming requests in Apache HTTP Server, which is essential for proper routing and handling of HTTP traffic.

Let's say, a service available on the http://127.0.0.1:8181 and Apache running it is available on http://192.168.0.15:80. "example1.com" domain points to http://192.168.0.15:80.

Here's an example of how you can configure ProxyPass directives:

```
vim /etc/httpd/conf.d/vhosts.conf


<VirtualHost *:80>
        ServerName example1.com
        ServerAlias www.example1.com
        ServerAdmin webmaster@example1.com

        ### Disable ProxyRequests (Forward Proxy):
        ProxyRequests Off

        ### Enable ProxyPreserveHost:
        ProxyPreserveHost On

        ### ProxyPass configuration (Reverse Proxy):
        ProxyPass / http://127.0.0.1:8181
        ProxyPassReverse / http://127.0.0.1:8181

        RequestHeader set Host example1.com

        ### Context Path From URL:
        #ProxyPass / http://127.0.0.1:8181/app1/
        #ProxyPassReverse / http://127.0.0.1:8181/app1/

        #RequestHeader set Host example1.com

        ### Context Path:
        #ProxyPass /app2 http://127.0.0.1:8181/app2/
        #ProxyPassReverse /app2 http://127.0.0.1:8181/app2/

        #RequestHeader set Host example1.com

        ErrorLog logs/example1.com.error_log
        CustomLog logs/example1.com.access_log common
</VirtualHost>

<VirtualHost *:80>
        ServerName example2.com
        ServerAlias www.example2.com
		ServerAdmin webmaster@example2.com
		
        ProxyRequests Off
        ProxyPreserveHost On
		
        ProxyPass / http://127.0.0.1:8182/
        ProxyPassReverse / http://127.0.0.1:8182/
		
        ErrorLog logs/example2.com.error_log
        CustomLog logs/example2.com.access_log common
</VirtualHost>

<VirtualHost *:80>
        ServerName example3.com
        ServerAlias www.example3.com
		ServerAdmin webmaster@example3.com
		
        ProxyRequests Off
        ProxyPreserveHost On
		
        ProxyPass / http://127.0.0.1:8183/
        ProxyPassReverse / http://127.0.0.1:8183/
		
        ErrorLog logs/example3.com.error_log
        CustomLog logs/example3.com.access_log common
</VirtualHost>


save and quite
```


```
apachectl configtest
```


```
systemctl restart httpd 
```


### Test:
Verify that the configuration is working by accessing the configured URLs (http://example1.com/, http://example2.com/) in a web browser.



### Configure load balancing: 
Setting up a load balancer with Apache HTTP Server (httpd) using ProxyPass involves distributing incoming requests across multiple backend servers to improve performance, reliability, and scalability. 
- The bytraffic value used here tries to ensure each of the servers handle an equal amount of traffic.
- The alternative  byrequests balancing method is a simpler version of bytraffic that gives each backend an equal share of the incoming requests.

Here's a basic guide to configure a simple load balancer with ProxyPass:

```
vim /etc/httpd/conf.d/vhost.conf

<VirtualHost *:80>
        ServerName example4.com
        ServerAlias www.example4.com
        ServerAdmin webmaster@example4.com

        ### Load Balancer configuration:
        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/

        #ProxyPassReverse / http://127.0.0.1:8181/
        #ProxyPassReverse / http://127.0.0.1:8182/

        <Proxy balancer://mycluster>
            ### Define backend servers:
            BalancerMember http://127.0.0.1:8181
            BalancerMember http://127.0.0.1:8182
            ProxySet lbmethod=byrequests
            #ProxySet lbmethod=bytraffic
        </Proxy>
        ### End ###

        ErrorLog /var/log/httpd/error.log
        CustomLog /var/log/httpd/access.log common

</VirtualHost>
```

```
systemctl restart httpd
```



Overall, ProxyPass is a powerful tool for setting up reverse proxying in Apache HTTP Server, allowing you to efficiently distribute incoming requests to backend servers or services


### Links:

- [Apache Module mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html)





