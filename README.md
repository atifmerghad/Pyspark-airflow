# Pyspark-airflow
<p>How to Schedule Pyspark Airflow Jobs</p>

## PySpark Project
![Apache Airflow](https://img.shields.io/badge/Apache%20Airflow-017CEE?style=for-the-badge&logo=Apache%20Airflow&logoColor=white)

## Airflow to Schedule Spark Jobs (Scheduling Spark Airflow Jobs)

```py
from airflow import DAG
from airflow.operators.python import PythonOperator
from airflow.contrib.operators.spark_submit_operator import SparkSubmitOperator

from datetime import datetime, timedelta

def message():
    print("Pyspark airflow job scheduling ...")

default_args = {
    'owner': 'atif',
    'start_date': datetime(2022,5,1),
    'retries': 0,
    'catchup': False,
    'retry_delay': timedelta(minutes=5),
}

dag= DAG('pyspark_job', default_args= default_args, schedule_interval= '*/1 * * * *')

python_operator= PythonOperator(task_id= 'message', python_callable= message, dag= dag)

spark_config= {
    'conf': {
        "spark.yarn.maxAppAttemps": "1",
        "spark.yarn.executor.memoryOverhead": "512"
    },
    'conn_id': 'spark_local',
    'application': '/Users/atif/Projects/ATIF/pyspark-etl/jobs/etl_job.py', #TODO
    'py_files': '/Users/atif/Projects/ATIF/pyspark-etl/packages.zip',
    'files': '/Users/atif/Projects/ATIF/python-examples/pyspark-etl/configs/etl_config.json',
    'driver_memory': '1g',
    'executor_cores': 1,
    'num_executors': 1,
    'executor_memory': '1g'
}

spark_operator= SparkSubmitOperator(task_id= 'spark_submit_task',dag= dag, **spark_config)

python_operator.set_downstream(spark_operator)

if __name__== "__main__":
    dag.cli()
```
