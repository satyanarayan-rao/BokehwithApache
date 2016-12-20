# Bokeh server with Apache 
Here I'll explain the process of incorporating Bokeh python based server in Apache web-server

Bokeh is an excellent server to display python based plots on browser. 
A better introduction can be seen [here](http://bokeh.pydata.org/en/latest/docs/user_guide/quickstart.html#userguide-quickstart). 

Now I'll explain how to enable Bokeh behind Apache web-server to serve this to a larger audience. 
In the first part, I'll show an sample Bokeh application running on its own. The second part will be integrating the same 
in the Apache web-server.  
## Bokeh End (An application) 
I am reciting a code from Bokeh example app named, **OHLC**. 
I have a directory with main.py containing the code of OHLC implementation. 
I am using here the directory format of Bokeh Application, 
please see [here] ( http://bokeh.pydata.org/en/latest/docs/user_guide/server.html#directory-format) for more detail.
To run the Bokeh serve all by itself with this application, please do the  following: 

```bash 
$ pwd 
/var/www/BokehwithApache
$ cd .. 
$ bokeh serve --show BokehwithApache 

```
Above invoked command will open a session in your default browser with a plot (meaning not important here).

The next target to get it running behind Apache and one should be able to see the OHLC plots by typing something like this in the browser.

```
http://127.0.0.1/BokehwithApache
http://127.0.0.1:8080/BokehwithApache
```
## Apache End 

+ Here I am assuming the following: 
- You don't have any public domain, basically you are working on localhost. 
- Apache is listening over two ports, 80 and 8080
- If you are naming any virtual host (www.abc.me), it has to be resolved in your /etc/hosts file 


You basically have to configure a virtual host, and Apache has a very easy way to do it. 

Create a file in the ``sites-available`` directory of Apache installation. And it should look like the following: 
```bash 
$ cat BokehwithApache.conf 
<VirtualHost 127.0.0.1:8080>
    ServerName 127.0.0.1

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    ProxyPreserveHost On
    ProxyPass /BokehwithApache/ws ws://127.0.0.1:5100/BokehwithApache/ws
    ProxyPassReverse /BokehwithApache/ws ws://127.0.0.1:5100/BokehwithApache/ws

    ProxyPass /BokehwithApache http://127.0.0.1:5100/BokehwithApache
    ProxyPassReverse /BokehwithApache http://127.0.0.1:5100/BokehwithApache

    <Directory />
        Require all granted
        Options -Indexes
    </Directory>
    
    Alias /static /var/www/BokehwithApache/static/
    <Directory /var/www/BokehwithApache/static/>
        # directives to effect the static directory
        Options +Indexes
    </Directory>

</VirtualHost>

``` 
