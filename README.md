# Setup script for nmon analysis environment by Redash with Docker on Ubuntu 18.04 and CentOS 7.

This is a setup scripts for nmon analysis environment by Redash on a Ubuntu 18.04 or CentOS 7 server, which uses Docker and Docker Compose for deployment and management.

## How to use
### Install and Initialize
1. Prepare Ubuntu 18.04 or CentOS 7 server. 
1. Clone this repository to `/opt`.
1. Run `/opt/nmon-redash/setup.sh`.
1. Wait for installation complete.
1. Access http://\<YOUR-HOST\>/ and complete redash initialization.
1. Register data source.
   1. Select type "PostgreSQL"
   1. Type database information and save. 
       - Host : postgres-nmon
       - Port : 5432
       - User : postgres
       - Password : password
       - Database Name : nmon

### Load Data
1. On your Windows client, convert raw nmon data into xlsx files using [nmonAnalyzer](https://www.ibm.com/developerworks/community/wikis/home?lang=en#!/wiki/W8214c473fef0_444f_886a_cd015ca34c89/page/nmon%E3%80%81nmon%20analyser)
1. Upload converted xlsx files to `/opt/nmon-redash/embulk/data/excel/server1`
1. Run `/opt/nmon-redash/bin/bulkload`

### Analyze Nmon Data
You can use redash feature freely to analyze nmon data by writing SQL, configuring visualization, creating dashboard. 


## Admin Operation
### Redo Bulkload
All loaded files are moved to `/opt/nmon-redash/embulk/data/excel/old`. So you have to re-distribute these files before redoing bulkload.  
1. Run `/opt/nmon-redash/bin/clear-db`.
1. Run `/opt/nmon-redash/bin/restore-excel`.
1. Run `/opt/nmon-redash/bin/bulkload`.

### Backup Redash
Redash configuration is automatically backuped at 0:00 AM on every Sunday. You can also backup the configuration manually by a command `/opt/nmon-redash/bin/backup-redash`.  
NOTE: You **must not** delete or edit `/opt/nmon-redash/env` file in order to avoid invalidating your backup data.  

### Restore Redash
Run `/opt/nmon-redash/bin/restore-redash`.

### Migrate to Another Server
1. Install this repository to the new server. 
1. Copy these files from old server to new one. 
  * `/opt/nmon-redash/env`
  * `/opt/nmon-redash/postgres-redash-data`
  * `/opt/nmon-redash/postgres-nmon-data`
  * `/opt/nmon-redash/embulk/data/excel`
3. Run `/opt/nmon-redash/setup.sh` at the new server. 


## Customize
### Add Another Table
1. Add load configuration file into `/opt/nmon-redash/embulk/data/embulk`.
1. Edit job configuration file at `/opt/nmon-redash/embulk/bulkload.dig`.
1. Add table definition sql file into `/opt/nmon-redash/postgres-nmon/scripts`.
1. Add the table name into the list at `/opt/nmon-redash/bin/clear-db`.
1. Run command `docker-compose -f /opt/nmon-redash/data/docker-compose.yml down`.
1. Run command `sudo rm -rf /opt/nmon-redash/postgres-nmon-data`.
1. Run `/opt/nmon-redash/setup.sh`.


## FAQ

### Can I use this in production?

For small scale deployments -- yes. But for larger deployments we recommend at least splitting the database (and probably Redis) into its own server (preferably a managed service like RDS) and setting up at least 2 servers for Redash for redundancy. You will also need to tweak the number of workers based on your usage patterns.

### How do I upgrade to newer versions of Redash?

See [Upgrade Guide](https://redash.io/help/open-source/admin-guide/how-to-upgrade).

### How do I use `setup.sh` on a different operating system?

You will need to update the `install_docker` function and maybe other functions as well.
