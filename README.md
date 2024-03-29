# Snowflake to Azure Databricks Data Ingestion

```bash
Snowflake Database -> Snowflake JDBC Driver -> Azure Databricks (Spark Cluster)
                                  |
                                  |  
                        Python Script (snowflake_to_databricks.py)
                                  |
                                  |
                             Parquet File
                                  |
                                  |
                           Azure Storage

```

This project provides an example implementation for reading data from Snowflake into Azure Databricks. It leverages Snowflake's JDBC connector and the PySpark API provided by Databricks to establish a connection, execute queries, and fetch data from Snowflake into Databricks.



## Prerequisites

- Azure Databricks workspace with an active cluster.
- Snowflake account and access credentials.
- Snowflake JDBC driver (downloaded and available in your environment).


## Setup Instructions

Upload the Snowflake JDBC driver JAR file to your Databricks workspace. You can do this by navigating to the Workspace sidebar, selecting your cluster, and clicking on Create > Library. Choose the uploaded JAR file and attach it to your cluster.

Copy the config_template.py file and rename it to config.py:

```bash
cp config_template.py config.py
```

Edit the config.py file and update the Snowflake connection details, such as the account URL, username, password, database, and warehouse.

Run the snowflake_to_databricks.py script in your Azure Databricks notebook or as a job. The script establishes a connection to Snowflake, executes a sample query, and fetches the results into a PySpark DataFrame.

## Project Structure

The project structure is as follows:

```bash
snowflake-to-azure-databricks/
  |-- config.py                 # Configuration file for Snowflake connection
  |-- snowflake_to_databricks.py # Main script for reading data from Snowflake
  |-- README.md                 # Project documentation
```

### config.py

```python
# Snowflake connection details
SNOWFLAKE_CONFIG = {
    'account': 'your-account-url',
    'username': 'your-username',
    'password': 'your-password',
    'database': 'your-database',
    'warehouse': 'your-warehouse'
}
```

### snowflake_to_databricks.py

This script performs the following tasks:

- Establishes a connection to Snowflake using the Snowflake JDBC driver.

- Executes a SQL query in Snowflake.

- Fetches the query results from Snowflake into a PySpark DataFrame in Azure Databricks.

- Displays the fetched data in the PySpark DataFrame.

The script uses the Snowflake JDBC driver to connect to the Snowflake account specified in the config.py file. It then executes the SQL query specified in the query variable, which can be customized according to your specific requirements. The script fetches the results of the query into a PySpark DataFrame using the createDataFrame() method, leveraging the Snowflake connector's integration with PySpark. Finally, it displays the data in the DataFrame using the show() method.

By running this script in your Azure Databricks environment, you can read data from Snowflake and process it within Databricks using PySpark's powerful data manipulation and analysis capabilities.

```python
from pyspark.sql import SparkSession
from snowflake.connector import connect
from config import SNOWFLAKE_CONFIG

# Snowflake connection details
ACCOUNT = SNOWFLAKE_CONFIG['account']
USERNAME = SNOWFLAKE_CONFIG['username']
PASSWORD = SNOWFLAKE_CONFIG['password']
DATABASE = SNOWFLAKE_CONFIG['database']
WAREHOUSE = SNOWFLAKE_CONFIG['warehouse']

# Snowflake JDBC URL
SNOWFLAKE_JDBC_URL = f"jdbc:snowflake://{ACCOUNT}.snowflakecomputing.com"

# Spark session
spark = SparkSession.builder.getOrCreate()

def read_from_snowflake(query):
    """
    Function to read data from Snowflake into a PySpark DataFrame.
    """
    # Create a Snowflake connection
    connection = connect(
        user=USERNAME,
        password=PASSWORD,
        account=ACCOUNT,
        warehouse=WAREHOUSE,
        database=DATABASE
    )
    
    # Execute the query and fetch the results
    cursor = connection.cursor()
    cursor.execute(query)
    results = cursor.fetchall()
    
    # Create a DataFrame from the results
    df = spark.createDataFrame(results, cursor.description)
    
    # Close the connection
    cursor.close()
    connection.close()
    
    return df

# Example query
query = "SELECT * FROM your_table_name"

# Read data from Snowflake into a DataFrame
df = read_from_snowflake(query)

# Display the DataFrame
df.show()
```

## Another config example

```python
# Snowflake connection details
SNOWFLAKE_CONFIG = {
    'account': 'your-account-url',
    'username': 'your-username',
    'password': 'your-password',
    'database': 'your-database',
    'warehouse': 'your-warehouse',
    'schema': 'your-schema',
    'role': 'your-role',
    'timezone': 'your-timezone',
    'query_result_format': 'your-result-format',
    'query_result_format_version': 'your-result-format-version',
    'query_result_s3_bucket': 'your-result-s3-bucket',
    'query_result_s3_path': 'your-result-s3-path',
}
```

In this example, additional parameters are included for the Snowflake connection:

**schema:** The Snowflake schema to use.

**role:** The Snowflake role to use.

**timezone:** The timezone for Snowflake connections.

**query_result_format:** The result format for Snowflake query results.

**query_result_format_version:** The version of the result format.

**query_result_s3_bucket:** The S3 bucket to store Snowflake query results.

**query_result_s3_path:** The S3 path to store Snowflake query results.
