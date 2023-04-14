# This repo builds and deploys a mysql stack service that can optionally be initialized from backup dump file(s)
## Folder structure:
* .secrets
   * .gitignore
* mysql
   * src
     * .gitignore

The .gitignore files ensure folder contents are never published to github by only publishing the .gitignore file itself

## Build and deploy mysql stack service using docker build secrets
1. Login to ssh terminal on swarm server
2. Change directory
```sh
    cd /opt/docker
```
3. Clone mysql repo
```sh
    git clone https://github.com/keramsey/mysql.git mysql
```
4. Change to project directory
```sh
    cd /opt/docker/mysql
```
4. Copy database dump file (sql, sql.gz) to mysql/src folder (optional)
5. Create secrets files for MYSQL_ROOT_PASSWORD, MYSQL_DATABASE, MYSQL_USER, MYSQL_PASSWORD
```sh
    nano .secrets/mysql_root
    nano .secrets/mysql_db
    nano .secrets/mysql_usr
    nano .secrets/mysql_pw
```
       Note: Add desired secret value to each secret file before saving. Do not surround values with quotes

6. Build docker image using 4 build secrets created in step 5 - update image name and tag as needed
```sh
    DOCKER_BUILDKIT=1 docker build --no-cache -t "mysql:1.0.0" --secret id=mysql_root,src=/opt/docker/mysql/.secrets/.mysql_root --secret id=mysql_db,src=/opt/docker/mysql/.secrets/.mysql_db --secret id=mysql_usr,src=/opt/docker/mysql/.secrets/.mysql_usr --secret id=mysql_pw,src=/opt/docker/mysql/.secrets/.mysql_pw ./mysql
```
7. Deploy stack service
```sh
    docker stack deploy -c docker-compose.yml mysql
```