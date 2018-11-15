Installing SquashTM on Debian and PostgreSQL database
=====================================================

Requirements
-------------
- OS: Debian 8 (oldstable, aka Jessie) or Debian 9 (stable, aka Stretch)
- DB engine: postgreSQL (9.6 recommended)
- Middleware: Java 1.8 + (openjdk 8 or Oracle java 8)
- Hardware: 2GB RAM, 2 cores CPU, 8 GB disk 

Installating squash-tm package
------------------------------
1. Add squashtest repo:
 sudo echo "deb http://repo.squashtest.org/debian jessie main" > /etc/apt/sources.list.d/squash-tm.list # (Debian 8)
or
 sudo echo "deb http://repo.squashtest.org/debian stretch main" > /etc/apt/sources.list.d/squash-tm.list # (Debian 9)
Note: From squash-tm 1.19, Debian 8 users will also need backports repository in order to meet squash-tm dependencies to dbconfig-common (>2.04):
 sudo echo "deb http://ftp.debian.org/debian jessie-backports main" > /etc/apt/sources.list
2. Import GPG key:
 wget -q -O - http://repo.squashtest.org/repo.squashtest.org.gpg.key | apt-key add - 
3. Update repositories:
 sudo apt update
4. Install squash-tm:
 sudo apt install squash-tm # Debian 9
or:
 sudo apt -t jessie-backports install squash-tm # Debian 8
 
Configuration
-------------
During installation of squash-tm package, users will be prompted with some questions regarding:
- squash-tm HTTP port (default: 8080)
- database type (Note: unless H2 is chosen, users will be asked once more about which dbconfig connector should be used)

If the chosen database type is mySQL or PostgreSQL, users will be presented the possibility to automatically configure SquashTM database with dbconfig.

If PostgreSQL is chosen, users will be asked to provide a database name, a username and password (used only by squash-tm to connect with its database). Additional options, such as database location, port (or socket), and administrative user password, should normally not be prompted to users, unless dpkg-reconfigure squash-tm is invoked.

Once installation is complete, squash-tm service is launched. Users can check the database connections settings in /etc/default/squash-tm file, and check the service status by running systemctl status squash-tm or service squash-tm status, and/or check /var/log/squash-tm/squash-tm.log file.


Alternative configuration (or squash-tm < 1.19)
-----------------------------------------------
Older squash-tm packages do not fully support database auto configuration, and do not even ship with postgreSQL scripts. Users can still use old squash-tm versions on postgreSQL database by following these steps:

1. Preparing SquashTM database and user:
 sudo su - postgres
 psql
 CREATE DATABASE "squashtm" WITH ENCODING='UTF8'; 
 CREATE USER "squash-tm" WITH PASSWORD 'initial_pw', LOGIN; 
 GRANT ALL PRIVILEGES ON DATABASE "squashtm" TO "squash-tm"; 
 \q

2. Configure postgreSQL so that squash-tm user can connect to squashtm database:
According to their needs, users should edit /etc/postgresql/<pg_version>/main/pg_hba.conf. Usually, right before the line stating local databases connection mode, adding a line stating:
 local squashtm squash-tm md5
is enough.

3. Run PostgreSQL initialization script on the database:
Users will first need to download the script according to squash-tm version, at https://repo.squashtest.org
Then, users have to execute the script against squashtm database:
 su - squash-tm # if squash-tm user has already been created, which is done during the installation of squash-tm package (selecting h2 database during configuration)
 psql -U squash-tm -d squashtm < path/to/postgresql/initialization/script
 
4. Manually edit /etc/default/squash-tm file:
 DB_URL="jdbc:postgresql://localhost:5432/squashtm"
 DB_TYPE="postgresql"
 DB_USERNAME="squash-tm"
 DB_PASSWORD="initial_pw"

5. Restart squash-tm service:
 sudo systemctl restart squash-tm
or:
 sudo service squash-tm restart
 
Image
-----

.. image:: template/picture.jpg
   :width: 200px
   :height: 100px
   :scale: 50 %
   :alt: alternate text
   :align: right
