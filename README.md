# MongoDB Installation and Configuration Guide

## Prerequisites

Ensure you have the following installed:
- A Linux-based operating system (Ubuntu/Debian)
- `sudo` privileges on your machine

## Installation

### 1. Update System Packages

```bash
sudo apt update
sudo apt upgrade -y
```

### 2. Install MongoDB

Add the MongoDB APT repository and install MongoDB:

```bash
sudo apt install -y gnupg curl
curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg \
   --dearmor
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
sudo apt update
sudo apt install -y mongodb-org
```

### 3. Start MongoDB Service

Start the MongoDB service and enable it to start on boot:

```bash
sudo systemctl start mongod
sudo systemctl enable mongod
```

## Configuration

### 1. Edit MongoDB Configuration File

Open the MongoDB configuration file using `nano`:

```bash
sudo nano /etc/mongod.conf
```

Add or modify the following configuration settings:

```yaml
# Where and how to store data.
storage:
  dbPath: /var/lib/mongodb

# Where to write logging data.
systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongod.log

# Network interfaces
net:
  bindIp: 0.0.0.0
  port: 27017

# How the process runs
processManagement:
  timeZoneInfo: /usr/share/zoneinfo

security:
  authorization: "enabled"
```

Save and close the file (Ctrl + X, then Y, then Enter).

### 2. Set Permissions

Ensure that MongoDB has the proper permissions to access its directories:

```bash
sudo chown -R mongodb:mongodb /var/lib/mongodb
sudo chown -R mongodb:mongodb /var/log/mongodb
```

### 3. Restart MongoDB Service

Apply the configuration changes by restarting MongoDB:

```bash
sudo systemctl restart mongod
```

## Creating a User

### 1. Connect to MongoDB

Open the MongoDB shell (`mongosh`):

```bash
mongosh
```

### 2. Create the Root User

Switch to the `admin` database and create a user with root privileges:

```js
use admin
db.createUser({
  user: "root",
  pwd: "root",
  roles: [
    { role: "root", db: "admin" },
    { role: "clusterMonitor", db: "admin" },
    { role: "dbAdminAnyDatabase", db: "admin" },
    { role: "readWriteAnyDatabase", db: "admin" },
    { role: "userAdminAnyDatabase", db: "admin" }
  ]
})
```

### 3. Authenticate as the New User

Authenticate with the new user:

```js
db.auth("root", "root")
```

### 4. Verify User Creation

Check the created users:

```js
db.getUsers()
db.getUser("root")
```

## Checking MongoDB Status

Verify that MongoDB is running correctly:

```bash
sudo systemctl status mongod
```

## Troubleshooting

- **MongoDB Not Starting**: Check logs in `/var/log/mongodb/mongod.log` for errors.
- **Authentication Issues**: Ensure the user credentials are correct and that you are connecting to the correct database.

## Conclusion

MongoDB is now installed, configured, and secured with user authentication. You can now start using MongoDB for your applications.

## Refereance
https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/
