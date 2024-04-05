# Install MongoDB Community Edition on Ubuntu(master/slave/backup/restore)

## **Install MongoDB Community Edition**

Follow these steps to install MongoDB Community Edition using the `apt` package manager.

From a terminal, install `gnupg` and `curl` if they are not already available:

To import the MongoDB public GPG key, run the following command:

```bash
sudo apt-get install gnupg curl
```

To import the MongoDB public GPG key, run the following command:

```bash
curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg \
   --dearmor
```

## **Create a list file for MongoDB**

Create the list file `/etc/apt/sources.list.d/mongodb-org-7.0.list` for your version of Ubuntu.

```bash
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
```

## **Reload local package database**

```bash
sudo apt-get update
```

## **Install the MongoDB packages**

```bash
sudo apt-get install -y mongodb-org
```

Optional. Although you can specify any available version of MongoDB, `apt-get` will upgrade the packages when a newer version becomes available. To prevent unintended upgrades, you can pin the package at the currently installed version:

```bash
echo "mongodb-org hold" | sudo dpkg --set-selections
echo "mongodb-org-database hold" | sudo dpkg --set-selections
echo "mongodb-org-server hold" | sudo dpkg --set-selections
echo "mongodb-mongosh hold" | sudo dpkg --set-selections
echo "mongodb-org-mongos hold" | sudo dpkg --set-selections
echo "mongodb-org-tools hold" | sudo dpkg --set-selections
```

# **How to Set Up MySQL Master-Slave Replication**

# **Configure the Source Database**

```bash
#master server
vim /etc/mongod.conf

net:
  port: 27017
  bindIp: 10.0.0.4

replication:
  replSetName: "RS01"
```

```bash
#slave server
vim /etc/mongod.conf

net:
  port: 27017
  bindIp: 10.0.0.5

replication:
  replSetName: "RS01"
```

After you finish editing the file, save and exit the file.

Restart the Mongodb server to apply the changes:

```bash
mrdex@srv1:~$ sudo systemctl restart mongod.service
[sudo] password for mrdex:
mrdex@srv1:~$ sudo systemctl status mongod.service
● mongod.service - MongoDB Database Server
     Loaded: loaded (/lib/systemd/system/mongod.service; disabled; vendor preset: enabled)
     Active: active (running) since Sun 2024-03-31 07:01:15 UTC; 10s ago
       Docs: https://docs.mongodb.org/manual
   Main PID: 4369 (mongod)
     Memory: 173.1M
        CPU: 3.354s
     CGroup: /system.slice/mongod.service
             └─4369 /usr/bin/mongod --config /etc/mongod.conf

Mar 31 07:01:15 srv1 systemd[1]: Started MongoDB Database Server.
Mar 31 07:01:15 srv1 mongod[4369]: {"t":{"$date":"2024-03-31T07:01:15.732Z"},"s":"I",  "c":"CONTROL",  "id":7484500, "ctx":"main","msg":"Environment vari>

```

login master server :

```bash

mongosh 10.0.0.4

Current Mongosh Log ID: 66090ba6a6d46f5d457b2da8
Connecting to:          mongodb://10.0.0.4:27017/?directConnection=true&appName=mongosh+2.2.2
Using MongoDB:          7.0.7
Using Mongosh:          2.2.2

For mongosh info see: https://docs.mongodb.com/mongodb-shell/

To help improve our products, anonymous usage data is collected and sent to MongoDB periodically (https://www.mongodb.com/legal/privacy-policy).
You can opt-out by running the disableTelemetry() command.
   The server generated these startup warnings when booting
   2024-03-31T07:01:15.858+00:00: Using the XFS filesystem is strongly recommended with the WiredTiger storage engine. See http://dochub.mongodb.org/core/prodnotes-filesystem
   2024-03-31T07:01:18.380+00:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
   2024-03-31T07:01:18.381+00:00: vm.max_map_count is too low

 
```

The command `rs.initiate()` in MongoDB is used to initialize a Replica Set. A Replica Set is a set of MongoDB instances that synchronizes data across multiple MongoDB servers to provide workload distribution, increased data availability, and establish a stable and scalable system.

```bash
rs.initiate()
```

The command `rs.add()` in MongoDB is used to add a member to a Replica Set. With this command, you can add another MongoDB server to your Replica Set.

```bash
#add slave server

rs.add("10.0.0.5")
```