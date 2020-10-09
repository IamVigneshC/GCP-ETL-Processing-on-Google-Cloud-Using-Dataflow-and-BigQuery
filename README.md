# GCP-ETL-Processing-on-Google-Cloud-Using-Dataflow-and-BigQuery


## Check project permissions
Ensure that your project has the correct permissions within Identity and Access Management (IAM).

In the Google Cloud console, on the Navigation menu (nav-menu.png), click IAM & Admin > IAM.

Confirm that the default compute Service Account {project-number}-compute@developer.gserviceaccount.com is present and has the editor role assigned. The account prefix is the project number, which you can find on Navigation menu > Home.

check-sa.png

If the account is not present in IAM or does not have the editor role, follow the steps below to assign the required role.

In the Google Cloud console, on the Navigation menu, click Home.

Copy the project number (e.g. 729328892908).

On the Navigation menu, click IAM & Admin > IAM.

At the top of the IAM page, click Add.

For New members, type:

{project-number}-compute@developer.gserviceaccount.com

Replace {project-number} with your project number.

For Role, select Project > Editor. Click Save.

Download the Starter Code
Open a session in Cloud Shell and store the GCP_PROJECT_ID in the PROJECT environment variable :

`export PROJECT=$DEVSHELL_PROJECT_ID`

Run the following command to copy and expand the source code for the lab into your Cloud Shell session:

` gsutil cp gs://$PROJECT-source/python-dataflow-examples.tar.gz . `
` tar -zxvf python-dataflow-examples.tar.gz `

Create a Cloud Storage bucket
Use the make bucket command to create a new regional bucket in the us-central1 region within your project:

` gsutil mb -c regional -l us-central1 gs://$PROJECT `

Create a Cloud Storage bucket
Copy files to your bucket
Use the gsutil command to copy sample data files to the Cloud Storage bucket you just created:

` gsutil cp gs://cloud-training/CBL047/usa_names.csv gs://$PROJECT/data_files/ `
` gsutil cp gs://cloud-training/CBL047/head_usa_names.csv gs://$PROJECT/data_files/ `


Copy files to your bucket
Create the BigQuery dataset
Create a dataset in BigQuery called lake. This is where all of your tables will be loaded in BigQuery:

` bq mk lake `

Create the BigQuery dataset (name: lake)

## Build a Dataflow pipeline
In this section you will create an append-only Dataflow which will ingest data into the BigQuery table. You can use the built-in code editor which will allow you to view and edit the code in the Cloud Console.


### Step 1 - Open the Cloud Shell code editor

Navigate to the source code by clicking on the Open Editor icon on the Cloud Shell ribbon:

Note: If prompted click on Open in New Window. It will open the code editor in new window.


### Step 2 - Data ingestion

You will now build a Dataflow pipeline with a TextIO source and a BigQueryIO destination to ingest data into BigQuery. More specifically, it will:

Ingest the files from Cloud Storage.
Filter out the header row in the files.
Convert the lines read to dictionary objects.
Output the rows to BigQuery.
Review pipeline Python code

In the Cloud Shell code editor navigate to training-data-analyst > courses > dw-pso > dataflow_python_examples and open the data_ingestion.py file. Read through the comments in the file, which explain what the code is doing. This code will populate the data in BigQuery.

Run the Apache Beam pipeline
Return to your Google Cloud Shell session for this step. You will now do a bit of setup for the required python libraries.

Run the following to set up the python environment:

` cd training-data-analyst/courses/dw-pso/dataflow_python_examples/ `

` sudo pip3 install apache-beam[gcp] `

You will run the Dataflow pipeline in the cloud.

Enter the following command to run the pipeline. This will spin up the workers required, run the code and shut them down when complete:

python3 data_ingestion.py \
--project=$PROJECT \
--max_num_workers=2 \
--runner=DataflowRunner \
--region us-central1 \
--staging_location=gs://$PROJECT/test \
--temp_location gs://$PROJECT/test \
--input gs://$PROJECT/data_files/head_usa_names.csv \
--save_main_session

Return to the Cloud Console and open the Navigation menu > Dataflow to view the status of your job.


Click on the name of your job to watch it's progress. Once your Job Status is Succeeded, navigate to BigQuery (Navigation menu > BigQuery) see that your data has been populated.

Click on your project name to see the usa_names table under the lake dataset. If you don't see the new table name at this stage you may need to refresh the page.

Note: If you don't see the usa_names table, try refreshing the page or view the tables using the classic BigQuery UI.
Click on the table then navigate to the Preview tab to see examples of the usa_names data.


### Step 3 - Data transformation

You will now build a Dataflow pipeline with a TextIO source and a BigQueryIO destination to ingest data into BigQuery. More specifically, you will:

Ingest the files from Cloud Storage.
Convert the lines read to dictionary objects.
Transform the data which contains the year to a format BigQuery understands as a date.
Output the rows to BigQuery.
Review pipeline Python code
Navigate to data_transformation.py and open it in the Cloud Shell code editor. Read through the comments in the file which explain what the code is doing.

Run the Apache Beam pipeline
You will run the Dataflow pipeline in the cloud. This will spin up the workers required, and shut them down when complete.

Run the following commands to do so:

python3 data_transformation.py \
--project=$PROJECT \
--max_num_workers=2 \
--runner=DataflowRunner \
--region us-central1 \
--staging_location=gs://$PROJECT/test \
--temp_location gs://$PROJECT/test \
--input gs://$PROJECT/data_files/head_usa_names.csv \
--save_main_session
Navigate to Navigation menu > Dataflow and click on the name of this job view the status.

When your Job Status is Succeeded in the Dataflow Job status screen, navigate to BigQuery to check to see that your data has been populated.

You should see the usa_names_transformed table under the lake dataset.

Click on the table and navigate to the Preview tab to see examples of the usa_names_transformed data.

Note: If you don't see the usa_names_transformed table, try refreshing the page or view the tables using the classic BigQuery UI.


### Step 4 - Data enrichment
You will now build a Dataflow pipeline with a TextIO source and a BigQueryIO destination to ingest data into BigQuery. More specifically, you will:

Ingest the files from Cloud Storage.
Filter out the header row in the files.
Convert the lines read to dictionary objects.
Output the rows to BigQuery.
Review pipeline Python code
Navigate to data_enrichment.py and open it in the Cloud Shell code editor. Check out the comments which explain what the code is doing. This code will populate the data in BigQuery.

Run the Apache Beam pipeline
Here you'll run the Dataflow pipeline in the cloud. Run the following to spin up the workers required, and shut them down when complete:

python3 data_enrichment.py \
--project=$PROJECT \
--max_num_workers=2 \
--runner=DataflowRunner \
--region us-central1 \
--staging_location=gs://$PROJECT/test \
--temp_location gs://$PROJECT/test \
--input gs://$PROJECT/data_files/head_usa_names.csv \
--save_main_session
Navigate to Navigation menu > Dataflow to view the status of your job.

Once your Job Status is Succeed in the Dataflow Job status screen, navigate to BigQuery to check to see that your data has been populated.

You should see the usa_names_enriched table under the lake dataset.

Click on the table and navigate to the Preview tab to see examples of the usa_names_enriched data.


### Step 5 - Data lake to Mart
You will now build a Dataflow pipeline with a TextIO source and a BigQueryIO destination to ingest data into BigQuery. More specifically, you will:

Ingest the files from Cloud Storage.
Filter out the header row in the files.
Convert the lines read to dictionary objects.
Output the rows to BigQuery.
Review pipeline Python code
Navigate to data_lake_to_mart.py and open it in the Cloud Shell code editor. Read through the comments in the file which explain what the code is doing. This code will populate the data in BigQuery.

Run the Apache Beam pipeline
Now you'll run the Dataflow pipeline in the cloud. Run the following to spin up the workers required, and shut them down when complete:

python3 data_lake_to_mart.py \
--worker_disk_type="compute.googleapis.com/projects//zones//diskTypes/pd-ssd" \
--max_num_workers=2 \
--project=$PROJECT \
--runner=DataflowRunner \
--region us-central1 \
--staging_location=gs://$PROJECT/test \
--temp_location gs://$PROJECT/test \
--save_main_session
Navigate to Navigation menu > Dataflow and click on the name of this new job to view the status.

Once you've Job Status is Succeeded in the Dataflow Job status screen, navigate to BigQuery to check to see that your data has been populated.

You should see the orders_denormalized_sideinput table under the lake dataset.

Click on the table and navigate to the Preview section to see examples of orders_denormalized_sideinput data.


### Step 6 - Data lake to Mart CoGroupByKey
You will now build a Dataflow pipeline with a TextIO source and a BigQueryIO destination to ingest data into BigQuery. More specifically, you will:

Ingest the files from Cloud Storage.
Filter out the header row in the files.
Convert the lines read to dictionary objects.
Output the rows to BigQuery.
Review pipeline Python code
Navigate to data_lake_to_mart_cogroupbykey.py and open it in the Cloud Shell code editor. Read through the comments which explain what the code is doing. Also review the Apache Beam Programming Guide for more advanced concepts. This code will populate the data in BigQuery.

Run the Apache Beam pipeline
Here you'll run the Dataflow pipeline in the cloud. Run the following to spin up the workers required, and shut them down when complete:

python3 data_lake_to_mart_cogroupbykey.py \
--worker_disk_type="compute.googleapis.com/projects//zones//diskTypes/pd-ssd" \
--max_num_workers=2 \
--project=$PROJECT \
--runner=DataflowRunner \
--region us-central1 \
--staging_location=gs://$PROJECT/test \
--temp_location gs://$PROJECT/test \
--save_main_session
Navigate to Navigation menu > Dataflow and click on the name of this new job to view the status.

Once your Job Status is Succeeded in the Dataflow Job status screen, navigate to BigQuery to check to see that your data has been populated.

You should see the orders_denormalized_cogroupbykey table under the lake dataset.

Click on the table and navigate to the Preview tab to see examples of the orders_denormalized_cogroupbykey data.


