//================================================================
//                          docker
//================================================================
export MY_DOCKER_IMG="img-mysql"
export MY_DOCKER_COND="cond-mysql"

# build image
docker build -f Dockerfile -t $MY_DOCKER_IMG .

# create container
docker run -d -it --name $MY_DOCKER_COND -p 43306:3306 -p 48080:8080 -p 422:22 $MY_DOCKER_IMG
docker attach $MY_DOCKER_COND

# start container
docker start $MY_DOCKER_COND

# query images & containers
docker images -a
docker ps -a

# remove container & image
docker stop $MY_DOCKER_COND
docker rm $MY_DOCKER_COND
docker rmi $MY_DOCKER_IMG

//================================================================
//                          configuration
//================================================================
Once you have created container from image, do below

//---------------------------------------
    1. locale configuration
//---------------------------------------
$ sudo dpkg-reconfigure tzdata

//---------------------------------------
    2. configure mysql
//---------------------------------------
$ mysql --version

$ vim /etc/mysql/mysql.conf.d/mysqld.cnf

    # /////////////////
    #bind-address           = 127.0.0.1
    bind-address           = 0.0.0.0
    # /////////////////

$ service mysql restart

$ mysql -u root -p

    # just press enter for password asking
    # /////////////////
    use mysql
    UPDATE user set host='%' where host='localhost';
    UPDATE user set plugin='mysql_native_password' where user='root';
    flush privileges;

    select user, host, plugin from user;
    exit;
    # /////////////////

$ mysql_secure_installation

    Press y|Y for Yes, any other key for No: y
    Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 1
    Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
    Disallow root login remotely? (Press y|Y for Yes, any other key for No) : n
    Remove test database and access to it? (Press y|Y for Yes, any other key for No) : n
    Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y


$ mysql -u root -p

    # just press enter for password asking
    # /////////////////
    SHOW VARIABLES LIKE 'validate_password%';
    SET GLOBAL validate_password.policy=LOW;
    ALTER USER 'root'@'%' IDENTIFIED BY '00000000';
    flush privileges;

    exit;
    # /////////////////
    # after this, need to enter valid password


//================================================================
//                          test
//================================================================
# ssh (initial password was defined on Dockerfile as ARG_PASSWD variable)
- user: root
- pw: 32143214
- port: 422

# mysql using workbench
- user: root
- pw: 00000000
- port: 43306

# change mysql root password
$ mysql -u root -p

    # if you try to change password first time, press '00000000'(current password) for password asking
    # then, you can replace password as you want on end of ALTER USER command instead of 00000000
    # /////////////////
    ALTER USER 'root'@'%' IDENTIFIED BY '00000000';
    flush privileges;

    exit;
    # /////////////////


