API endpoint:
/var/www/api/
   api.py
   api.wsgi
   
   api.py:
   ```
    from flask import Flask


    app = Flask(__name__)

    @app.route('/')
    def hello_world():
        return 'API is working.'


    if __name__ == '__main__':
        app.run(debug=True,host='0.0.0.0')
   ```
   
   api.wsgi:
   ```
    #!/usr/bin/python

    import sys
    sys.path.insert(0, '/var/www/api/')

    from api import app as application
   ```

Apache conf:
/etc/apache2/sites-available/
  api.conf
  
  api.conf:
  ```
    <VirtualHost *:80>
	    ServerName localhost
	    ServerAdmin shashank@localhost
	    WSGIScriptAlias / /var/www/api/api.wsgi
	    <Directory /var/www/api/>
		      Order allow,deny
		      Allow from all
	    </Directory>
	    ErrorLog ${APACHE_LOG_DIR}/error.log
	    LogLevel warn
	    CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
    
    <VirtualHost *:443>
        ServerName localhost
        ServerAdmin shashank@localhost
        WSGIScriptAlias / /var/www/api/api.wsgi
        <Directory /var/www/api/>
                Order allow,deny
                Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined

	SSLEngine on
        SSLCertificateFile /etc/apache2/ssl/apache.crt
        SSLCertificateKeyFile /etc/apache2/ssl/apache.key

     </VirtualHost>
   ```
   
   default-ssl.conf
   ```
   	<IfModule mod_ssl.c>
    		<VirtualHost _default_:443>
        		ServerAdmin shashank@localhost
        		ServerName localhost
       			ServerAlias localhost
        		DocumentRoot /var/www/api/
        		ErrorLog ${APACHE_LOG_DIR}/error.log
        		CustomLog ${APACHE_LOG_DIR}/access.log combined
        		SSLEngine on
        		SSLCertificateFile /etc/apache2/ssl/apache.crt
        		SSLCertificateKeyFile /etc/apache2/ssl/apache.key
        		<FilesMatch "\.(cgi|shtml|phtml|php)$">
                        	SSLOptions +StdEnvVars
        		</FilesMatch>
        		<Directory /usr/lib/cgi-bin>
                        	SSLOptions +StdEnvVars
        		</Directory>
        		BrowserMatch "MSIE [2-6]" \
                        	nokeepalive ssl-unclean-shutdown \
                        	downgrade-1.0 force-response-1.0
        		BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown
     		</VirtualHost>
    </IfModule>
```

```
sudo apt-get update
sudo apt-get install apache2
sudo apt-get install libapache2-mod-wsgi python-dev
sudo a2enmod wsgi
sudo mkdir /var/www/api
sudo vi /var/www/api/api.py # Copy contents
sudo vi /var/www/api/api.wsgi # Copy contents
sudo vi /etc/apache2/sites-available/api.conf # Copy Contents
cd /var/www/api
sudo a2ensite api
sudo service apache2 restart
/etc/init.d/apache2 restart
sudo a2enmod ssl
sudo service apache2 restart
/etc/init.d/apache2 restart
sudo mkdir /etc/apache2/ssl
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt
sudo vi /etc/apache2/sites-available/default-ssl.conf # Copy contents
sudo a2ensite default-ssl.conf
sudo service apache2 restart
/etc/init.d/apache2 restart
```

mod_wsgi - https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
SSL - https://www.digitalocean.com/community/tutorials/how-to-create-a-ssl-certificate-on-apache-for-ubuntu-14-04
