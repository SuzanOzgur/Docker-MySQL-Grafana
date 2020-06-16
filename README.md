# Docker-MySQL-Grafana

Commands to run separate images for MySQL and Grafana with persistent data are provided below.


__WARNING:__ the -v flag will create the directories if they don't exist on the host machine.

`$PWD` variable is the directory from which the docker commands are executed.

/volumes/mysql must be created by user before starting mysql docker to avoid permission issues


Folder contents:
* __volumes/grafana__: grafana plugins, dashboards and data sources
* __volumes/mysql__: all MySQL data


### MySQL


If no MySQL-server is installed on the host machine,
add the ports so that MySQL-server is accesible by MySQLWorkbench at localhost:3306

If no other image runs in docker, the IP given to MySQL will be 172.17.0.2

```bash
docker run -p 3306:3306 --user $(id -u):$(id -g) --name some-mysql -v $PWD/volumes/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root -d mysql:latest
```

### Grafana

```bash
docker run -d -p 3000:3000 --name grafana --user $(id -u):$(id -g) -v $PWD/volumes/grafana:/var/lib/grafana grafana/grafana:6.5.0
```

To install plugins run:
```bash
docker exec -u $(id -u) -it grafana grafana-cli plugins install grafana-piechart-panel
docker restart grafana
```

To embed iframes run the following:

```bash
# Replace whole line
docker exec -u root -it grafana sed -i 's/;allow_embedding = false/allow_embedding = true/' /etc/grafana/grafana.ini
# Replace line after occurence
docker exec -u root -it grafana sed -i '/enable anonymous access/{n;s/.*/enabled = true/}' /etc/grafana/grafana.ini
```
