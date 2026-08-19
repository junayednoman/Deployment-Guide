## Install PostgreSQL

**1. Update apt & install PostgreSQL**

```bash
sudo apt update
sudo apt install postgresql postgresql-contrib -y
```

Check if it’s running:

```bash
sudo systemctl status postgresql
```

**2. Switch to the postgres user**
```bash
sudo -i -u postgres
```

**3. Create a new database and user**
```bash
# Open PostgreSQL prompt
psql

# Inside psql:
CREATE DATABASE mydb;
CREATE USER myuser WITH ENCRYPTED PASSWORD 'mypassword';
GRANT ALL PRIVILEGES ON DATABASE mydb TO myuser;
GRANT ALL ON SCHEMA public TO myuser;

-- Make your user the explicit owner of the schema
ALTER SCHEMA public OWNER TO myuser;


# Exit
\q

```

## Configure PostgreSQL to accept external connections

**1. Edit postgresql.conf to listen on all interfaces:**
```bash
sudo nano /etc/postgresql/14/main/postgresql.conf
```

Find listen_addresses and change:
```bash
listen_addresses = '*'
```

**2. Edit pg_hba.conf to allow your IP or all:**
```bash
sudo nano /etc/postgresql/14/main/pg_hba.conf
```
Add:
```bash
host    all     all     0.0.0.0/0     md5
```

**3. Restart PostgreSQL:**
```bash
sudo systemctl restart postgresql
```
