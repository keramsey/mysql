### Build and deploy a mysql stack service initialized using build secrets and backup dump file(s)
## Folder structure:
* .secrets
   * .gitignore
* mysql
   * src
     * .gitignore
   * Dockerfile
* docker-compose.yml
* README.md

The .gitignore files ensure folder contents are never published to github. The .gitignore file itself is the only file in folder that is published to github

## Build and deploy mysql stack service using docker build secrets
1. Login to ssh terminal (swarm server)
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
5. Copy database dump files (sql, sql.gz) to mysql/src folder (/opt/docker/mysql/mysql/src). Multiple dump files will be restored to mysql image in alphanumeric order, by file name
6. Create 4 secrets files (MYSQL_ROOT_PASSWORD, MYSQL_DATABASE, MYSQL_USER, MYSQL_PASSWORD)
```sh
nano .secrets/mysql_root
nano .secrets/mysql_db
nano .secrets/mysql_usr
nano .secrets/mysql_pw
```
    Note: Add desired secret value to each file before saving - Do not surround values with quotes
      mysql_root: MYSQL_ROOT_PASSWORD
      mysql_root: MYSQL_DATABASE
      mysql_usr:  MYSQL_USER
      mysql_pw:   MYSQL_PASSWORD

7. Build docker image using 4 build secrets created in step 5 - update image name and tag as needed
```sh
DOCKER_BUILDKIT=1 docker build --no-cache -t "mysql:1.0.0" --secret id=mysql_root,src=/opt/docker/mysql/.secrets/.mysql_root --secret id=mysql_db,src=/opt/docker/mysql/.secrets/.mysql_db --secret id=mysql_usr,src=/opt/docker/mysql/.secrets/.mysql_usr --secret id=mysql_pw,src=/opt/docker/mysql/.secrets/.mysql_pw ./mysql
```
8. Deploy stack service (mysql_mysql)
```sh
docker stack deploy -c docker-compose.yml mysql
```