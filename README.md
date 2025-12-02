# Data Engineering project with sample spotify Data from https://listenbrainz.org/ in Json format

Project Structure
* `task1_elt.ipynb`: The main Jupyter notebook with ETL Transformations for task 1.
* `task2_analysis.ipynb`: Jupyter notebook containing SQL Statements for task 2.
* `dataset.parquet`: The source data.
* `database/sample.db`: Duckdb DataBase.
* `Dockerfile`, `docker-compose.yml`, `requirements.txt`: Configuration for the reproducible environment.


How to Run (Using Docker)
Prerequisite: Docker Desktop must be installed.

1. Unzip the project folder.
2. Open a terminal in the folder.
3. Run the following command:
   ```bash
   docker compose up --build```
4. Open either http://localhost:8888 or check in Vs Code: "Dev Containers" extension, unter /workspace directory.

Or check check the Script and html output for task 2.


General logic for transformation (task1_etl.ipynb)

Idempotency & Hashing: The script generates a deterministic SHA-1 hash for every record (column id) based on key fields (track metadata, listen time, user). This serves as a primary key to ensure records are not duplicated during re-runs.

Normalization: Nested JSON structures (specifically track_metadata) are flattened and normalized into distinct columns to make the data queryable.

Strict Validation: A custom validation layer filters the dataset. It enforces schema rules, such as ensuring IDs are 36-character strings, timestamps are valid, and numerical values (like duration) fall within logical ranges. Invalid rows are discarded to maintain data quality.

Incremental Loading: The validated data is loaded into the stg.raw table in DuckDB. The script uses a WHERE NOT EXISTS clause to perform an incremental load, adding only new records that do not already exist in the database.
