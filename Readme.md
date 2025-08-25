# Slowly Changing Dimension Type 2 (SCD-2)
<p align="center">
  
</p>

*“Our Ready-to-Use SCD-2 Framework”*

The SCD-2 logic in this framework enables tracking historical changes in staging tables while preserving previous versions of records. This ensures accurate auditing and historical reporting.

## Overview

**Purpose:**
- Manage incremental updates to staging tables while preserving historical data.

**Scope:**
- Only tables and columns defined in the configuration file are processed.

**Metadata_columns:**

- is_active – indicates if the record is the current active version.

- effective_from_date – start date of record validity.

- effective_to_date – end date of record validity.

## What Users Need to Configure
- **Tables**: Only include tables that require SCD-2 processing.

- **Columns**: Specify the columns you want tracked. The framework ignores irrelevant columns.

- **Primary Key & Incremental Column**: Ensure these are correctly defined to detect changes accurately.

No changes to the underlying code are needed—the framework handles all SCD-2 logic automatically.


## Function Reference
**process_table**:

*Purpose* - Initiates the SCD process for a given table configuration.

*Inputs* :

- row: A configuration record containing: Source and sink schemas 
  - Table names 
  - Primary key 
  - Incremental column (e.g., timestamp columns) 
  - Selected columns for tracking
 
*Behavior* :

- Reads source table. 
- Skips processing if source is empty. 
- Calls the scd() function to proceed with loading and transformation


**scd**:

*Purpose* - Controls the overall logic for SCD processing, deciding between full and incremental loads.

*Inputs* :

- Source/sink schema and table names 
- Primary key 
- Incremental column 
- Selected columns

*Outputs* : 

- Delegates to full load or SCD Type 2 update. 
- Appends log entries for success, skip, or failure. 


*Behavior* :


- If the sink table does not exist, or if new columns are added to the selected columns, a full load is triggered. Additionally, if the table does not have a primary key defined, a full load will also occur.
- Otherwise, it performs incremental change detection and Type 2 updates. 
- Logs the outcome of the operation



**scd_initial_full_load**:

*Purpose* -  Performs the first-time load of the source data into the sink with SCD tracking.

*Inputs* :

- Source schema and table 
- Sink schema and table 
- DataFrame with source data 
- Incremental column 
- Primary key 
- Selected columns 

*Outputs* : 
- Writes all rows to the sink with metadata. 
- Appends a full-load log entry

*Behavior* :


- Deduplicates records using primary key and timestamp. 
- Adds columns like __is_active, __effective_from_date.  
- Overwrites the sink table. 



**scd_type_2**:

*Purpose* -  Implements Slowly Changing Dimension Type 2 logic for incremental loads.

*Inputs* :


- Source data (new and historical) 
- Sink data 
- Primary key 
- Incremental column 
- Schema and table names 


*Outputs* : 

- Writes merged results to the sink table. 
- Logs row counts for new, updated, deleted, inactive


*Behavior* :


- Identifies: 
   - New records 
   - Updated records (by checksum) 
   - Soft deletes (inactive) 
   - Hard deletes 
- Updates historical records’ validity (__effective_to_date) and status


| Function                           | Purpose                                                              |
|--------------------------------    |-----------------------------------------------------------------------------|
| `table_exists(..)`                 | Checks if a Delta table exists in the lakehouse                      |
| `fetch_incremental_records(...)`   | Retrieves records newer than the last loaded timestamp               |
| `fetch_deleted_records(...)`       | Retrieves records that were soft deleted after the last known delete | 
| `select_columns(...)`              | Selects a fixed set of columns from a DataFrame                      |
| `add_hash_col(...)`                | Adds a row_check_sum column for change detection                     |
| `generate_primary_key(...)`        | Builds a single key for both simple and composite primary keys       |
| `add_scd_columns(...)`             | Adds SCD2 metadata columns like _is_active, _effective_from_date,etc |
                     



