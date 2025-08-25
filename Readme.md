# Slowly Changing Dimension Type 2 (SCD-2)
<p align="center">
  
</p>

*“Our Ready-to-Use SCD-2 Implementation for Reliable Data Versioning and Change Tracking”*

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
