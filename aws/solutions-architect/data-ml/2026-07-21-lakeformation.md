## Lake Formation
- [Overview](#overview)
- [How it Works](#how-it-works)

### Overview

* `Lake Formation` a fully managed service that simplifies building, securing, and managing data lakes on Amazon S3. It centralizes governance, automates data ingestion and cleansing, and enforces fine-grained, column- and row-level security across AWS analytics and machine learning services
    - automates data ingestion, deduplication, and transformation
    - policies are defined in a single location and automatically enforced across other tools (athena, redshift, quicksight)
    - securely share cataloged datasets across different aws accounts, orgs, without moving the data
* Purpose is aggregation of data in a meaningful way
    - works with resources `aws glue etl` to source and enrich data
    - gets stored and processed in `lake formation` to be shipped to services like `athena`

### How it Works

1. Define a data location:
    - register `s3 buckets` with lake formation to establish a central data repo
2. Build a data catalog:
    - use integrated `aws glue crawler`s to auto discover, classify, and create a centralized metadata catalog
3. Configure permissions:
    - grant data access (at db, table, or column/row levels) to specific users and roles using the `lake formation` console
4. Analyze Data:
    - query the secured data using analytics tools like `athena` or `redshift spectrum`(allows you to run sql queries against data in s3 without needing to load or transform data into local `dwh` cluster -- saves costs)
* NOTE: allows cross regiona erplication, to ship data to another `s3` location
    - great for resiliency and redundency