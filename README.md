# configurare-prestashop-su-docker

Guida youtube :

https://www.youtube.com/watch?v=jQjTzQxG6OA

Addons Prestashop


_________


https://addons.prestashop.com/it/

_________



## Prestashop docker compose


```
services:
  prestashop:
    image: prestashop/prestashop
    environment:
      - DB_SERVER=db
      - DB_NAME=prestashop
      - DB_USER=prestashop
      - DB_PASSWD=prestashop #change this
      - PS_DEV_MODE=0
      - PS_DEMO_MODE=0
      - PS_INSTALL_AUTO=1
      - PS_DOMAIN=yourdomain.com #change this domain to yours
      - PS_FOLDER_ADMIN=admin
      - ADMIN_MAIL=demo@prestashop.com
      - ADMIN_PASSWD=prestashop_demo
    links:
      - db
    depends_on:
      - db
    ports:
      - 8080:80
    networks:
      - prestashop-net
    healthcheck:
      test: [ "CMD", "curl", "-f", "https://yourdomain.com" ] #change this domain to yours
      interval: 30s
      timeout: 10s
      retries: 5
    volumes:
      - /home/docker/prestashop:/var/www/html

  db:
    image: mysql:5.7
    command: --default-authentication-plugin=mysql_native_password
    environment:
      - MYSQL_ROOT_PASSWORD=admin #change this
      - MYSQL_DATABASE=prestashop
      - MYSQL_USER=prestashop
      - MYSQL_PASSWORD=prestashop #this should match the DB_PASSWD in the prestashop container above
    networks:
      - prestashop-net
    volumes:
      - /home/docker/db:/var/lib/mysql
      
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    links:
      - db:db
    ports:
      - 1234:80
    depends_on:
      - db
    networks:
      - prestashop-net     

networks:
  prestashop-net:
```


## Nginx proxy manager docker compose

```
services:
  npm:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
    volumes:
      - npmdata:/data
      - npmssl:/etc/letsencrypt
    networks:
      - prestashop_prestashop-net

networks:
  prestashop_prestashop-net: #modify to network in portainer
    external: true

volumes:
  npmdata:
  npmssl:

```



