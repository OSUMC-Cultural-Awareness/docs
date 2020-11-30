[Link to our production cluster](https://cloud.mongodb.com/v2/5fbd1d47d242c47aa1327aa3#clusters)  
contact @freeman91 for login credentials

# Mongo Atlas setup 
[Mongo Atlas Getting Started](https://docs.atlas.mongodb.com/tutorial/deploy-free-tier-cluster/)

1. create account
2. deploy a free tier cluster
3. added my personal ip and the public ip of our prod ec2 instance in Network Access
4. created an ec2-user in Database users
5. used our db backup file to create our db in the atlas cluster
6. connect to the db using the `MONGO_URI` in .env on production

```sh
MONGO_URI='mongodb+srv://<user>:<password>@data-cluster.tjzlp.mongodb.net/database?retryWrites=true&w=majority'
```

- if your machine has mongodb installed connect your shell to the cluster with:\
```sh
mongo "mongodb+srv://<user>:<password>@data-cluster.tjzlp.mongodb.net/<dbname>"
```
log into Mongo cluster to add users, or contact @freeman91 for credentials

# Backup production database
```sh
mongodump --uri="mongodb+srv://<user>:<password>@data-cluster.tjzlp.mongodb.net" --archive='db-backup.bak'
```


# Restore production database from a backup
```sh
mongorestore "mongodb+srv://<user>:<password>@data-cluster.tjzlp.mongodb.net" --archive='db-backup.bak'
```