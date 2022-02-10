# Initial use
## Docker Image
```bash
docker pull wuye88/cvat:1.7
```
## 1.Create a mount directory
```bash
pwd=`pwd`

mkdir -p $pwd/{cvat_data/cache,cvat_keys,cvat_logs/migrations,cvat_share,cvat_postgresql_db,cvat_postgresql_run,cvat_nginx_log,cvat_nginx_run,cvat_nginx_conf,cvat_redis_run,cvat_redis_conf,cvat_ssh,cvat_tmp/supervisord,cvat_tmp/cvat-server,cvat_ui}

touch $pwd/cvat_logs/cvat_server.log

chmod -R 777 $pwd
```
## 2.Run the container
```bash
docker run -itd --name cvat_test -e LICO_CVAT_WEB_PREFIX="dev/cvat/test" -e CVAT_USERNAME="admin" -e CVAT_PASSWORD="admin" -p 8080:8000 -v $pwd/cvat_data:/home/django/data -v $pwd/cvat_keys:/home/django/keys -v $pwd/cvat_logs:/home/django/logs -v $pwd/cvat_share:/home/django/share -v $pwd/cvat_postgresql_db:/var/postgresql-data/ -v $pwd/cvat_postgresql_run:/var/run/postgresql/ -v $pwd/cvat_nginx_log:/var/log/nginx -v $pwd/cvat_nginx_run:/var/lib/nginx/ -v $pwd/cvat_nginx_conf:/etc/nginx/conf.d -v $pwd/cvat_redis_run:/var/lib/redis -v $pwd/cvat_redis_conf:/etc/redis -v $pwd/cvat_ssh:/home/django/.ssh -v $pwd/cvat_tmp:/tmp -v $pwd/cvat_ui:/usr/share/nginx/html wuye88/cvat:1.7

# To specify a service port, you need to specify the following environment variables:CVAT_NGINX_PORT,CVAT_REDIS_PORT,CVAT_POSTGRES_PORT,CVAT_SERVER_PORT,CVAT_NUCLIO_HOST
# If you need to use Models for automatic annotation, you need to install the CVAT official document to deploy Nuclio service. And specify the CVAT_NUCLIO_HOST environment variable when running the current container.
# For example:
docker run -itd --name cvat_test -e LICO_CVAT_WEB_PREFIX="dev/cvat/test" -e CVAT_NGINX_PORT="8000" -e CVAT_REDIS_PORT="6379" -e CVAT_POSTGRES_PORT="5432" -e CVAT_SERVER_PORT="8080" -e CVAT_NUCLIO_HOST="192.168.24.38" -p 8080:8000 -v $pwd/cvat_data:/home/django/data -v $pwd/cvat_keys:/home/django/keys -v $pwd/cvat_logs:/home/django/logs -v $pwd/cvat_share:/home/django/share -v $pwd/cvat_postgresql_db:/var/postgresql-data/ -v $pwd/cvat_postgresql_run:/var/run/postgresql/ -v $pwd/cvat_nginx_log:/var/log/nginx -v $pwd/cvat_nginx_run:/var/lib/nginx/ -v $pwd/cvat_nginx_conf:/etc/nginx/conf.d -v $pwd/cvat_redis_run:/var/lib/redis -v $pwd/cvat_redis_conf:/etc/redis -v $pwd/cvat_ssh:/home/django/.ssh -v $pwd/cvat_tmp:/tmp -v $pwd/cvat_ui:/usr/share/nginx/html wuye88/cvat:1.7
```
## 3.Start the cvat
```bash
# Inside the container
docker exec -it cvat_test bash
# Execute the following command
sed -i "s/;user=django/user=django/g" supervisord.conf
/usr/bin/supervisord
# If there is a nginx process, redis, postgres, apache2, start over.
```
## 4.Open the cvat
You should use a browser to access `http://<your_ip>:8080/dev/cvat/test/`.
## 5.The login
The default user name and password are admin.If you set `CVAT_USERNAME` or `CVAT_PASSWORD`, you need to log in with your username and password.

# Possible problems
## 1.Q:Page 404 after LICO_CVAT_WEB_PREFIX is specified
A:Because in the `/etc/nginx/conf. D/default` files. The path rules for ^ the conf/dev/cvat/([/ w] +) /, if you need customised LICO_CVAT_WEB_PREFIX, or match the rules, or modify the configuration files.
## 2.Q:Why use `sed -i "s/;user=django/user=django/g" supervisord.conf` at run time
A:This command is used only when you start the container as the root user.
