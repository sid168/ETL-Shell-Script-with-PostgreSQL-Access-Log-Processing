# ETL-Shell-Script-with-PostgreSQL-Access-Log-Processing
This project demonstrates an ETL (Extract, Transform, Load) process using a shell script to manage access logs. The script will download a gzipped file, extract relevant data, transform it into CSV format, and load it into a PostgreSQL database.


## Prerequisites
Ensure the following tools and services are available:
- PostgreSQL installed and running
- Shell scripting environment (e.g., Bash)
- PostgreSQL username and password
- `wget`, `gunzip`, and `psql` commands installed

## Steps Overview:
1. Download the data.
2. Extract relevant fields from the data.
3. Transform the data format to CSV.
4. Load the data into the PostgreSQL database.

## Instructions

### 1. Set Up PostgreSQL Database
- Start the PostgreSQL server.
- Connect to the `template1` database and create the `access_log` table to store the extracted data.

```bash
psql -U postgres -d template1
```

Create the table `access_log`:
```sql
CREATE TABLE access_log(
    timestamp TIMESTAMP, 
    latitude FLOAT, 
    longitude FLOAT, 
    visitor_id CHAR(37)
);
```

### 2. Shell Script: cp-access-log.sh

The following script performs the ETL process:

```bash
#!/bin/bash

# cp-access-log.sh
# This script downloads the file 'web-server-access-log.txt.gz', extracts data, 
# transforms it into CSV, and loads it into the PostgreSQL database.

# Download the access log file
wget "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Bash%20Scripting/ETL%20using%20shell%20scripting/web-server-access-log.txt.gz"

# Unzip the file to extract the .txt file
gunzip -f web-server-access-log.txt.gz

# Extract phase
echo "Extracting data"
# Extract columns 1 (timestamp), 2 (latitude), 3 (longitude), and 4 (visitorid)
cut -d"#" -f1-4 web-server-access-log.txt > extracted-data.txt

# Transform phase
echo "Transforming data"
# Replace the "#" delimiter with "," and write to CSV
tr "#" "," < extracted-data.txt > transformed-data.csv

# Load phase
echo "Loading data"
# Load the data into PostgreSQL
export PGPASSWORD=<your_password>  # Replace with your actual PostgreSQL password
echo "\c template1; \COPY access_log FROM '/path/to/transformed-data.csv' DELIMITERS ',' CSV HEADER;" | psql --username=postgres --host=localhost
```

### 3. Execute the Shell Script
Run the script to perform the ETL process:

```bash
bash cp-access-log.sh
```

### 4. Verify Data Load
After the script has finished executing, verify the data by running the following SQL query in PostgreSQL:

```sql
SELECT * FROM access_log;
```

You should see the data from the `transformed-data.csv` loaded into the `access_log` table.

## Next Steps --- 

- Modify the script to process additional columns from the log file.
- Automate the script to run at regular intervals using a cron job.
- Explore different PostgreSQL table structures to improve query performance.

