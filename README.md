# LinkedIn Learning: Hands-on Introduction to Data Engineering - Notes
```
python --version

# Install Apache Airflow
## Based on the Python version, use the following command to install Airflow:

export AIRFLOW_HOME="/workspaces/hands-on-introduction-data-engineering-4395021/airflow"
pip install "apache-airflow==2.6.3" --constraint "https://raw.githubusercontent.com/apache/airflow/constraints-2.6.3/constraints-3.10.txt"

#Viewing Airflow Functions
#To view the full set of available functions, use:

airflow

#Airflow Database Configuration
##By default, Airflow uses an SQLite database located at:

airflow/airflow.db

#Initializing the Database
airflow db init
Note: In real life, we would typically use AWS RDS or a standalone service, not SQLite.

#Creating an Admin User
airflow users create \
    --username admin \
    --firstname firstname \
    --lastname lastname \
    --role Admin \
    --email admin@example.com \
    --password your_password

#Webserver Configuration
1. Open webserver_config.py under the airflow directory.
2. Set WTF_CSRF_enabled to false.
##Running the Web Server
airflow webserver -d
Access the webserver via the URL format:

https://<your-airflow-instance>.app.github.dev/home
Scheduler Warning
If the scheduler is not running, start it using:

airflow scheduler
Stopping the Scheduler and Webserver
Find the process IDs for webserver and scheduler using:

ps aux | grep "airflow webserver" | grep -v grep
ps aux | grep "airflow scheduler" | grep -v grep
To stop the webserver and scheduler, use:



kill <PID>
or




pkill -9 -f "airflow"
Optimal Airflow Setup for Organization
To view where the airflow.cfg file is located, run:




echo $AIRFLOW_HOME
List available categories in airflow.cfg:




cat airflow/airflow.cfg | grep -o '\[.*\]'
Changing Example DAGS
To disable example DAGS, set load_examples to false in airflow.cfg and restart Airflow.

Task 1: Creating a Single-Node DAG
Check where Airflow is expecting the DAG to be:



cat airflow/airflow.cfg | grep "dags_folder"
Create a new .py file in the dags folder with a small message.
Run the DAG:




python -W ignore airflow/dags/one_task_dag.py
To view the message:



cat lab/temp/create-this-file.txt
Task 2: Creating a Two-Task DAG (ETL Example)
For the ETL example, follow these steps:

E: Extracting Data
Download the list of top-level domains:




wget -c https://datahub.io/core/top-level-domain-names/_r/-/data/top-level-domain-names.csv -O /workspaces/hands-on-introduction-data-engineering-4395021/lab/manual-extract-data.csv
T: Transforming Data (Finding Generic TLDs)
Using Python and Pandas:

python


from datetime import date
import pandas as pd

df = pd.read_csv("manual-extract-data.csv")
generic_type_df = df[df["Type"]=="generic"]

# Add today's date as a new column
today = date.today()
generic_type_df["Date"] = today.strftime("%Y-%m-%d")

# Save the transformed data
generic_type_df.to_csv("manual-transform-data.csv", index=False)
L: Loading Data into Database
Use SQLite:




sqlite3 manual-load-db.db
.mode csv
.import --skip 1 manual-transform-data.csv top_level_domains
Automating the Process
Create a DAG that automates the ETL process by linking all tasks (Extract, Transform, Load).
