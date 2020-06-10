# anneb/postgis

based on https://github.com/postgis/docker-postgis/tree/master/12-3.0/alpine

This image contains an alpine OS with empty postgres database and postgis installed

Data can be mounted on /var/lib/postgresql/data

Directories with data to be imported can be mounted on /import

Example for starting the database and populating with data:
```
#remove old volume data, do this only when needed!
docker volume rm -f pgdata

#start postgis with data directory bound to volume 'pgdata' and /downloads bound to /import
docker run --rm -d -p 5432:5432 --name postgisdb -e POSTGRES_PASSWORD=postgres -v pgdata:/var/lib/postgresql/data/ -v /downloads:/import anneb/postgis

#wait for the database to start up
docker exec -it postgisdb /bin/bash -c "until psql -U postgres -c 'select 1'; do sleep 1; done;"

#create postgis extension
docker exec -it postgisdb psql -U postgres -c "create extension postgis;"

#import data
docker exec -it postgisdb /bin/bash -c "psql -U postgres < /import/mydata.sql"

#stop the import container
docker stop postgisdb

#start a new container with populated data
docker run --rm -d -p 5432:5432 --name postgisdb -e POSTGRES_PASSWORD=postgres -v pgdata:/var/lib/postgresql/data/ anneb/postgis

```



