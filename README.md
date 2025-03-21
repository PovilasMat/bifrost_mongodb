Dump db to a folder
mongodump --host xxx.xxx.xxx.xxx --username admin --password xxx --authenticationDatabase admin --out /srv/data/DB/mongodump_backup

Restore db from a folder
docker run --rm --network container:mongodb -v /srv/tmp-data/mongodump_backup:/mnt/mongodump_backup mongo:4.4.23-focal mongorestore --username admin --password xxx --authenticationDatabase admin --host localhost --port 27017 --dir /mnt/mongodump_backup

#Tests to see if db was successfully restored
docker exec -it mongodb mongo -u admin -p xxx --authenticationDatabase admin
show dbs
#exit after

docker logs mongodb | tail -50
