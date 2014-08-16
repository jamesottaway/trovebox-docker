# Trovebox in Docker

This image has a dependency on MySQL, so the easiest way is to pull the official
MySQL image from the Docker registry and run it:

```
docker pull mysql
docker run --detach --name=mysql --env=MYSQL_ROOT_PASSWORD=password mysql
```

We now need to create the Trovebox user and database within MySQL:

```
docker run --rm --interactive --tty --link=mysql:mysql mysql sh -c \
  'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" \
  -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
```

Run the following SQL statements within the MySQL session:

```
CREATE USER 'trovebox' IDENTIFIED BY 'trovebox';
CREATE DATABASE IF NOT EXISTS 'trovebox';
GRANT ALL ON trovebox.* TO 'trovebox';
FLUSH PRIVILEGES;
```

We can now start Trovebox, link it to MySQL and publish it's internal port 80.

```
docker run --detach --link=mysql:mysql --name=trovebox --publish=8080:80 trovebox
```

When filling in the MySQL credentials, use the following command to determine
the correct value for the 'MySQL Host' field:

```
docker inspect -f "{{ .NetworkSettings.IPAddress }}" trovebox/mysql
```

When entering the Filesystem credentials, use `/trovebox/src/html/photos` as the
'File system root'.
