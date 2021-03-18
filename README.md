                                          ### wordpress-mariadb ###
                                          
                                         

# update des packages

    $ sudo apt update && sudo apt upgrade

# Téléchargement de l'archive Wordpress

    $ cd /var/www/html/
    
    $ wget https://wordpress.org/latest.tar.gz
    
    $ tar -xvzf latest.tar.gz

# copie de la configuration par defaut

    $ cd wordpress

    $ cp wp-config-sample.php wp-config.php

# ajout des infos pour la base de données MariaDB

    $ sudo nano wp-config.php

      // ** MySQL settings - You can get this info from your web host ** //
      /** The name of the database for WordPress */
      define( 'DB_NAME', 'wpdb' );

      /** MySQL database username */
      define( 'DB_USER', 'wpuser' );

      /** MySQL database password */
      define( 'DB_PASSWORD', 'dbpassword' );

      /** MySQL hostname */
      define( 'DB_HOST', 'localhost' );

# attribution des droits du dossier wordpress

    $ sudo chown -R www-data:www-data /var/www/html/wordpress

# configuration du nginx pour qu'il serve le site Wordpress

    $ sudo nano /etc/nginx/sites-available/wordpress.conf

# copier tout le bloc server dans le wordpress.conf

    server {
        listen 80;
        root /var/www/html/wordpress;
        index index.php index.html index.htm;
        server_name www.monsite.com;

        access_log /var/log/nginx/wordpress_access.log;
        error_log /var/log/nginx/wordpress_error.log;

        client_max_body_size 64M;

        location / {
            try_files $uri $uri/ /index.php?$args;
            }

        location ~ \.php$ {
            try_files $uri =404;
            include /etc/nginx/fastcgi_params;
            fastcgi_read_timeout 3600s;
            fastcgi_buffer_size 128k;
            fastcgi_buffers 4 128k;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_pass unix:/run/php/php7.3-fpm.sock;
            fastcgi_index index.php;
                }

            }

# on ajoute le nom de domaine dans le fichier host

    $ sudo nano /etc/hosts
    
        127.0.0.1 www.monsite.com
  

# création du lien symbolique vers le dossier sites-enabled

    $ sudo ln -s /etc/nginx/sites-available/wordpress.conf /etc/nginx/sites-enabled/

# On redémarre les services pour qu'ils tiennent compte des changements dans les configurations

    $ sudo systemctl restart nginx

    $ sudo systemctl restart php7.3-fpm
    
# aller sur le navigateur de la machine virtuelle à l'adresse www.monsite.com
