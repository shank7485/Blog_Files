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
   ```
   
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
