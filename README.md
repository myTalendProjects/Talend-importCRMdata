# Talend-importDMSdata
Talend Job to import DMS (Device management System) data in to Device Model master table in Postgres database and update Subscriber (Subscriber Profile) table. 

![alttext](./images/TalendJob.PNG?raw=true)


## Import and Build in Talend OpenStudio
This Talend project can be imported and build in Talned open studio for ESB.

![alttext](./images/ImportProject.PNG?raw=true)

## Prerequisites

### PostgreSQL database
In this project data in a csv fille list will be imported to PostgreSQL databse. 
A PostgreSQL database needs to be preconfigured. The database schema `(with sample data)` is included in [database-PostgreSQL](./database_PostgreSQL) directory.

## Project configuration

Context variables needs to be congured according to the envirionment as mentioned below

| Context Variable | Description  |
--- | --- 
| operator_id | DMS operator id|
| postgresHost | PostgreSQL database host IP| 
| postgresPort | PostgreSQL database port| 
| postgresUser | PostgreSQL database username| 
| postgresPass | PostgreSQL database password| 
| postgresDatabase | PostgreSQL database name| 
| postgresDeviceModel | Device Model table name |
| postgresSubscriber | Subscriber table name |
| dmsSkipped | Relative dir path to store skppied records |
| dmsInvalid | Relative dir path to store invalid records |
| dmsSource | Relative dir path to read source files |
| dmsCompleted | Relative dir path to store completed files |
| dmsRoot | Absolute dir path for root of the dir structure | 


`Example Configuration`

![alttext](./images/Talend-Context-Var.PNG?raw=true)

`Example Directory Structure`

![alttext](./images/Sample-Directory-Structure.PNG?raw=true)

A Sample data set is in  [my_sample_data](./my_sample_data) directory.


## How it works
Location configured as `dmsRoot` will be considered as the root directory for all the directory relative paths. 
1. CSV file list in location configured as `dmsSource` will be read sequencially and validate.
  - Validate against schema structure
  - Validate against data type/length constrains
  and invalid records will be written to the file with name `source_file_name + '_dms_invalid'`
2. Device Model master data table configured as `postgresDeviceModel` will be looked up by `Model` for data availability and insert if not available.
3. Subscriber table configured as `postgresSubscriber` will be looked up by `MSISDN` for data availability.
  - If MSISDN is available, database record will be updated with `primary-key` value of the `Model` in Device Model master data table.
  - Other wise file record will be skipped and relevant data will be written to the file with `source_file_name + '_dms_skipped'`
4. Completed file will be rename to `source_file_name + '_completed'` and moved to the location congired as `dmsCompleted`

## Output result files

Completed files will be renamed and move to location specified in `dmsCompleted` relative path.

![alttext](./images/Sample-Completed.PNG?raw=true)

Invalid recored in completed files will be renamed and move to location specified in `dmsInvalid`  relative path.

![alttext](./images/Sample-Invalid.PNG?raw=true)

Skipped recored in completed files will be renamed and move to location specified in `dmsSkipped`  relative path.

![alttext](./images/Sample-Skipped.PNG?raw=true)







