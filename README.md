# Talend-importCRMdata
Talend Job to import CRM (Customer Relationship Management System) data in to Subscriber (Subscriber Profile) table and update exiting Subscriber table records. 

![alttext](./images/TalendJob.PNG?raw=true)


## Import and Build in Talend OpenStudio
This [Talend project](./IMPORT_CRM_DATA) can be imported and build in Talend open studio for ESB.

![alttext](./images/ImportProject.PNG?raw=true)

## Prerequisites

### PostgreSQL database
In this project data in a csv file list will be imported to PostgreSQL database. 
A PostgreSQL database needs to be preconfigured. The database schema `(with sample data)` is included in [database-PostgreSQL](./database-PostgreSQL) directory.

`Sample subscriber table`

![alttext](./images/Postgres-Subscriber-Table.PNG?raw=true)

## Project configuration

Context variables needs to be congured according to the envirionment as mentioned below

| Context Variable | Description  |
--- | --- 
| operator_id | CRM operator id|
| postgresHost | PostgreSQL database host IP| 
| postgresPort | PostgreSQL database port| 
| postgresUser | PostgreSQL database username| 
| postgresPass | PostgreSQL database password| 
| postgresDatabase | PostgreSQL database name| 
| postgresDeviceModel | Device Model maser data table name |
| postgresSubscriberType | Subscriber Type master data table name |
| postgresSubscriberPrimaryOffer | Subscriber Primary Offer master data table name |
| postgresSubscriberStatus | Subscriber Primary Status master data table name |
| postgresSubscriber | Subscriber table name |
| crmSkipped | Relative dir path to store skppied records |
| crmInvalid | Relative dir path to store invalid records |
| crmSource | Relative dir path to read source files |
| crmCompleted | Relative dir path to store completed files |
| crmRoot | Absolute dir path for root of the dir structure | 


`Example Configuration`

![alttext](./images/Talend-Context-Var.PNG?raw=true)

`Example Directory Structure`

![alttext](./images/Sample-Directory-Structure.PNG?raw=true)

A Sample data set is in  [my_sample_data](./my_sample_data) directory.


## How it works
Location configured as `crmRoot` will be considered as the root directory for all the directory relative paths. 
1. CSV file list in location configured as `crmSource` will be read sequencially and validate.
  - Validate against schema structure
  - Validate against data type/length constrains
  and invalid records will be written to the file with name `source_file_name + '_crm_invalid'`
2. Master data tables configured will be looked up by for data availability and insert if not available.
  - If data is available, database record `primary-key` value of the master data table will be fetched and flow will be contnued.
  - Other wise file record will be skipped and relevant data will be written to the file with `source_file_name + '_crm_skipped'`
3. Subscriber table configured as `postgresSubscriber` will be looked up by `MSISDN` for data availability.
  - If MSISDN is available, database record will be updated with `primary-key` values of the master data tables.
  - If MSISDN is not available, database record will be inserted with `primary-key` values of the master data tables.
4. Completed file will be rename to `source_file_name + '_completed'` and moved to the location congired as `crmCompleted`

## Output result files

Completed files will be renamed and move to location specified in `crmCompleted` relative path.

![alttext](./images/Sample-Completed.PNG?raw=true)

Invalid recored in completed files will be renamed and move to location specified in `crmInvalid`  relative path.

![alttext](./images/Sample-Invalid.PNG?raw=true)

Skipped recored in completed files will be renamed and move to location specified in `crmSkipped`  relative path.

![alttext](./images/Sample-Skipped.PNG?raw=true)
