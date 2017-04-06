API endpoint:
/home/ubuntu/Assistant-for-Software-Defined-Infrastructure/
   api.py
   api.wsgi
   
   Add the following in api.wsgi
   api.wsgi:
   ```
    #!/usr/bin/python

    import sys
    sys.path.insert(0, '/home/ubuntu/Assistant-for-Software-Defined-Infrastructure/')

    from api import app as application
   ```

Apache conf:
/etc/apache2/sites-available/
  assistant.conf
  
  assistant.conf:
  ```
    <VirtualHost *:80>
	    ServerName localhost
	    ServerAdmin ubuntu@localhost
	    WSGIScriptAlias / /home/ubuntu/Assistant-for-Software-Defined-Infrastructure/api.wsgi
	    <Directory /home/ubuntu/Assistant-for-Software-Defined-Infrastructure/>
		      Require all granted
	    </Directory>
	    ErrorLog ${APACHE_LOG_DIR}/error.log
	    LogLevel warn
	    CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
    
    <VirtualHost *:443>
        ServerName localhost
        ServerAdmin ubuntu@localhost
        WSGIScriptAlias / /home/ubuntu/Assistant-for-Software-Defined-Infrastructure/api.wsgi
        <Directory /home/ubuntu/Assistant-for-Software-Defined-Infrastructure/>
		      Require all granted
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
        		ServerAdmin ubuntu@localhost
        		ServerName localhost
       			ServerAlias localhost
        		DocumentRoot /home/ubuntu/Assistant-for-Software-Defined-Infrastructure/
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
cd /home/ubuntu/
git clone https://github.com/shank7485/Assistant-for-Software-Defined-Infrastructure.git
# Copy api.wsgi contents
sudo vi /home/ubuntu/Assistant-for-Software-Defined-Infrastructure/api.wsgi
# Copy assistant.conf contents
sudo vi /etc/apache2/sites-available/assistant.conf
sudo a2ensite assistant
sudo service apache2 restart
/etc/init.d/apache2 restart
sudo a2enmod ssl
sudo service apache2 restart
/etc/init.d/apache2 restart
sudo mkdir /etc/apache2/ssl
# Generate Cert and Keys. (test keys)
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt
# Copy default-ssl.conf contents
sudo vi /etc/apache2/sites-available/default-ssl.conf
sudo a2ensite default-ssl.conf
sudo service apache2 restart
/etc/init.d/apache2 restart
```

mod_wsgi - https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
SSL - https://www.digitalocean.com/community/tutorials/how-to-create-a-ssl-certificate-on-apache-for-ubuntu-14-04
