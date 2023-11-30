DBAL Vertica driver
===================

Doctrine DBAL connector driver for Vertica.
*Ready for use in **Laravel** / Lumen*

Requirements
------------

* php >= 7.4
* php_odbc extension
* Vertica drivers
* Doctrine >=2 DBAL

Installation
------------
*Case for Ubuntu / Debian*

##### Vertica drivers:

```shell
# Download official Vertica ODBC driver
curl -OL https://www.vertica.com/client_drivers/12.0.x/12.0.4-0/vertica-client-12.0.4-0.x86_64.tar.gz

# Extract & install it
sudo tar -xvzf vertica-client-12.0.4-0.x86_64.tar.gz -C /

# Set config files
sudo printf "[VerticaDev]\nDriver = /opt/vertica/lib64/libverticaodbc.so\nPort = 5433\nDriver = Vertica" > /etc/odbc.ini
sudo printf "[Vertica]\nDriver = /opt/vertica/lib64/libverticaodbc.so" > /etc/odbcinst.ini
sudo printf "[Driver]\nDriverManagerEncoding=UTF-16\nODBCInstLib = /usr/lib/x86_64-linux-gnu/libodbcinst.so.1\nErrorMessagesPath=/opt/vertica/lib64\nLogLevel=4\nLogPath=/tmp" > /etc/vertica.ini
```

##### PHP extentions & ODBC unix client:

```bash
sudo apt-get install php-odbc php-pdo php-json unixodbcn
```

*Case for docker php8 image*

```shell
# Download official Vertica ODBC driver
RUN curl -OL https://www.vertica.com/client_drivers/23.4.x/23.4.0-0/vertica-client-23.4.0-0.x86_64.tar.gz
# Extract & install it
RUN mkdir -p /opt/vertica/ && \
    tar vzxf vertica-client-23.4.0-0.x86_64.tar.gz -C / && \
    rm vertica-client-23.4.0-0.x86_64.tar.gz

# Set config files
RUN printf "[VerticaDev]\nDriver = /opt/vertica/lib64/libverticaodbc.so\nPort = 5433\nDriver = Vertica" > /etc/odbc.ini && \
    printf "[Vertica]\nDriver = /opt/vertica/lib64/libverticaodbc.so" > /etc/odbcinst.ini && \
    printf "[Driver]\nDriverManagerEncoding=UTF-8\nODBCInstLib = /usr/lib/x86_64-linux-gnu/libodbcinst.so.1\nErrorMessagesPath=/opt/vertica/lib64\nLogLevel=4\nLogPath=/tmp" > /etc/vertica.ini
```

##### PHP extentions & ODBC unix client:

```bash
RUN set -ex; \
        docker-php-source extract; \
        { \
                echo '# https://github.com/docker-library/php/issues/103#issuecomment-271413933'; \
                echo 'AC_DEFUN([PHP_ALWAYS_SHARED],[])dnl'; \
                echo; \
                cat /usr/src/php/ext/odbc/config.m4; \
        } > temp.m4; \
        mv temp.m4 /usr/src/php/ext/odbc/config.m4; \
        apt-get update; \
        apt-get install -y --no-install-recommends unixodbc-dev; \
        docker-php-ext-configure odbc --with-unixODBC=shared,/usr; \
        docker-php-ext-install odbc; \
        docker-php-source delete;
```

##### PDO Connector compatible with Doctrine 2 DBAL:

TODO

```bash
# TODO update for symfony
composer require mixartemev/dbal-vertica-driver
```

Integration in Symfony
----------------------

##### .env

```dotenv
# either this
DB_HOST_VERTICA=127.0.0.1
DB_PORT_VERTICA=5433
DB_DATABASE_VERTICA=dbname
DB_USERNAME_VERTICA=username
DB_PASSWORD_VERTICA=password
DATABASE_URL_VERTICA=${}
```

##### config/packages/doctrine.yaml

```yaml
doctrine:
    dbal:
        default_connection: vertica
        connections:
            vertica:
                driver_class: Doctrine\DBAL\Driver\Vertica\VerticaDriver
                host: '%env(DB_HOST_VERTICA)%'
                port: '%env(int:DB_PORT_VERTICA)%'
                database: '%env(DB_DATABASE_VERTICA)%'
                username: '%env(DB_USERNAME_VERTICA)%'
                password: '%env(DB_PASSWORD_VERTICA)%'
```

[//]: # (##### app/Providers/AppServiceProvider.php)

[//]: # ()
[//]: # (```php)

[//]: # (<?php)

[//]: # (class AppServiceProvider extends ServiceProvider)

[//]: # ({)

[//]: # (    public function boot&#40;&#41;)

[//]: # (    {)

[//]: # (        App::bind&#40;'db.connector.vertica', function &#40;&#41; {)

[//]: # (            return new VerticaDriver;)

[//]: # (        }&#41;;)

[//]: # (        DB::resolverFor&#40;'vertica', function &#40;$connection, $database, $prefix, $config&#41; {)

[//]: # (            return new PostgresConnection&#40;$connection, $database, $prefix, $config&#41;;)

[//]: # (        }&#41;;)

[//]: # (    })

[//]: # (})
[//]: # (```)
