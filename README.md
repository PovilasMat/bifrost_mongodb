My mongodb was set up with local installed  4.4.3 code built from source, data is stored in /srv/data/DB/mongodb/. It is hosted on port 27017. The instance is ran through systemctl service. The OS was RHEL 7. I want to migrate the instance to my new server which is RHEL9, using newest possible 4.4.x through Docker. But I want it still be ran through systemctl service. Help me generate Dockerfile with all the parameters as well as systemctl service file with proper launch, exit as well as explain how to set up required networking for db to be accessible through 27017 port.

mongodump --host localhost --port 27017 --out /srv/data/mongodump_backup

rsync -avz /srv/data/mongodump_backup/ new_server:/srv/data/mongodump_backup/

ls -lah /srv/data/mongodump_backup/

docker-compose.yml

version: '3.8'

services:
  mongodb:
    image: mongo:4.4.23-focal  # Explicitly use Ubuntu 20.04 base image
    container_name: mongodb
    restart: always
    ports:
      - "27017:27017"
    volumes:
      - /srv/data/mongodb:/data/db
    environment:
      MONGO_INITDB_ROOT_USERNAME: "your_admin_user"
      MONGO_INITDB_ROOT_PASSWORD: "your_admin_password"
    command: ["--auth"]

docker-compose up -d
docker-compose down
docker-compose restart mongodb

sudo nano /etc/systemd/system/mongodb.service

[Unit]
Description=MongoDB (Docker Compose)
After=network.target docker.service
Requires=docker.service

[Service]
WorkingDirectory=/srv/mongodb
ExecStart=/usr/bin/docker-compose up -d
ExecStop=/usr/bin/docker-compose down
ExecReload=/usr/bin/docker-compose restart
Restart=always
TimeoutSec=300

[Install]
WantedBy=multi-user.target

sudo systemctl daemon-reload
sudo systemctl enable mongodb
sudo systemctl start mongodb

docker exec -it mongodb mongorestore --username your_admin_user --password your_admin_password --authenticationDatabase admin --dir /srv/data/mongodump_backup

docker exec -it mongodb mongo -u your_admin_user -p your_admin_password --authenticationDatabase admin

show dbs

use mydatabase

show collections
