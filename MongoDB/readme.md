# HANDLE MONGODB

## INSTALL MONGODB

```bash
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc |  gpg --dearmor | sudo tee /usr/share/keyrings/mongodb.gpg > /dev/null

echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list

sudo apt update

sudo apt install mongodb-org
```

# USING MONGODB

Run: `mongosh` to enter mongo shell

```mongosh
> use admin

// create admin user

> db.createUser(
  {
    user: "admin",
    pwd: passwordPrompt(), // or pwd: 'password'
    roles: [ { role: "userAdminAnyDatabase", db: "admin" }, "readWriteAnyDatabase" ]
  }
)

> db.adminCommand( { shutdown: 1 } )

> exit
```

To know the status of your mongod service run `systemctl status mongod`

If it is down or failing, run: `mongod --auth --port 27017 --dbpath /var/lib/mongodb`. Then search the log for error.

If this `"error":"IllegalOperation: Attempted to create a lock file on a read-only directory: /var/lib/mongodb"` is the error run the following command

```bash
sudo chown -R mongodb:mongodb /var/lib/mongodb

sudo chown mongodb:mongodb /tmp/mongodb-27017.sock

sudo service mongod restart

sudo systemctl status mongod
```

## ENABLE ACCESS CONTROL

Edit the config file `/etc/mongod.conf`

```bash
sudo vi /etc/mongod.conf

# make sure the following configuration exists

security:
    authorization: enabled
```

Now log in as the admin user

```bash
mongo --port 27017  --authenticationDatabase "admin" -u "admin" -p
```

You can now create databases and users.
