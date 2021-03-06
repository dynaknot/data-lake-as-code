# Installing Athena-ready Chemical and Genomics Databases from the  Registry of Open Data on AWS

The AWS Registry of Open Data (RODA) hosts the following datasets in a parquet format in a public S3 bucket which you are free to copy. 

However, in an effort to make these datasets more consumable, the CloudFormation templates below will create the corresponding AWS Glue database, tables, and schemas in your account's AWS Glue Data Catalog. 

This allows you to start querying the data with Athena directly out of the public S3 buckets, **in seconds**, with zero servers or networking to setup. 

![](http://devspacepaul.s3.us-west-2.amazonaws.com/dlac/howitworks.png)

Going further, you can use standard JDBC/ODBC to query these databases with your own notebooks, business inteligence tools, plotting software, Redshift or EMR clusters, or even your local development machine. 


## One time prerequisite 

If you have never used Amazon Athena in your account before, you need to [setup a default query location](https://docs.aws.amazon.com/athena/latest/ug/querying.html#query-results-specify-location-console). This should only take 2-3 minutes to do.

You only need to do this once. 

## Deploy this into my account

Click the links below for the data set you are interested in. Then click the "Create stack". Make sure you are in your preferred region. 


## Latest Versions:



### [Chembl 27 ![](https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?#/stacks/quickcreate?templateUrl=https%3A%2F%2Faws-roda-hcls-datalake.s3.amazonaws.com%2FChembl.27.RodaTemplate.json&stackName=Chembl27-RODA) 

### [Open Targets 20.06 ![](https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?#/stacks/quickcreate?templateUrl=https%3A%2F%2Faws-roda-hcls-datalake.s3.amazonaws.com%2FOpenTargets.20.06.RodaTemplate.json&stackName=OpenTargets-20-06-RODA)

### [BindingDB ![](https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?#/stacks/quickcreate?templateUrl=https%3A%2F%2Faws-roda-hcls-datalake.s3.amazonaws.com%2FBindingDbRodaTemplate.json&stackName=BindingDB-RODA)

### [Genome Tissue Expresssion Database ![](https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?#/stacks/quickcreate?templateUrl=https%3A%2F%2Faws-roda-hcls-datalake.s3.amazonaws.com%2FGTEx.8.RodaTemplate.json&stackName=GTEx-8-RODA)

It should take approximately 60 seconds for the stack to finish deploying.

The GTEx data set requires one extra step after the deployment. The `exon_reads` table has > 17k columns. Expressing all of those columns in YAML would exceed the CloudFormation max template length! Once the GTEx template deploys, go the [AWS Glue Console](https://us-west-2.console.aws.amazon.com/glue/home?#catalog:tab=crawlers), check the box next to the `gtex_8_awsroda_crawler` and click 'Run crawler'. Once it finishes (1-2 minutes) you can query the GTEx data just like the other datasets. 

## Query the data!

Go to the [Amazon Athena](https://console.aws.amazon.com/athena/home?force#query) console.

Select the database you just deployed in the "Database" drop down.

You should see the tables listed below. Expand the table to see the columns/schema for each table. You can also click on the vertical dots next to a table name and select 'Preview table' to get a quick feel for whats inside.

![](http://devspacepaul.s3.us-west-2.amazonaws.com/dlac/runquery.png)


## Want to know more?

### How this works
Take the time to visit the AWS Glue console. There, you will see the databases, tables, table definitions, etc. You will notice the locations for the tables are s3://aws-roda-hcls-datalake/database/etc. 

### More about the datasets:

These datasets were downloaded directly in thier original forms from the following locations. You should refer to the source documentation for the datasets below for more information about the data itself. We have not modified any datasets beyond converting them from thier orignal database dumps/json/csv/etc formats into a parquet representation for performance and cost efficency. 

[ChEMBL Source Data](https://chembl.gitbook.io/chembl-interface-documentation/downloads)

[Open Targets Source Data](https://www.targetvalidation.org/downloads/data)

[Binding DB Source Data](https://www.bindingdb.org/bind/chemsearch/marvin/SDFdownload.jsp?all_download=yes)

[GTEx Source Data](https://gtexportal.org/home/datasets)

### How were these datasets prepared?

Data sets in the AWS RODA HCLS Data Lake were created using the [Data Lake as Code Architecture (DLAC)](https://github.com/aws-samples/data-lake-as-code). The AWS RODA HCLS Data Lake tracks the [RODA branch](https://github.com/aws-samples/data-lake-as-code/tree/roda). The [DLAC mainline](https://github.com/aws-samples/data-lake-as-code/tree/mainline) branch is there to help you create your own data lake with your own private data sets using the DLAC framework.

### Older Versions:

[Deploy Open Targets 20.06](https://console.aws.amazon.com/cloudformation/home?#/stacks/quickcreate?templateUrl=https%3A%2F%2Faws-roda-hcls-datalake.s3.amazonaws.com%2FOpenTargets.19.11.RodaTemplate.json&stackName=OpenTargets-19-11-RODA)

[Deploy Chembl 25](https://console.aws.amazon.com/cloudformation/home?#/stacks/quickcreate?templateUrl=https%3A%2F%2Faws-roda-hcls-datalake.s3.amazonaws.com%2FChembl.25.RodaTemplate.json&stackName=Chembl25-RODA)

## Troubleshooting

**Your query has the following error(s):**
```
HIVE_CURSOR_ERROR: Can not read value at 9 in block 0 in file s3://aws-roda-hcls-datalake/...snappy.parquet
```

There is a bug in how Athena's Presto engine handles Hive's decimal type. This is fixed in an upcoming release. In the event you see this error, create a new workgroup in Athena called exactly `AmazonAthenaPreviewFunctionality` and then use that workgroup for your queries. Athena will use the the next generation Presto version and you shouldnt see this error again. 




