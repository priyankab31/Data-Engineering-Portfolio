"""
Project: End-to-End Data Engineering Pipeline
Author: Priya Bagade
Description: Main ETL pipeline execution
"""

import logging
from pathlib import Path

import pandas as pd

# Configure logging
logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s - %(levelname)s - %(message)s"
)

BASE_DIR = Path(__file__).resolve().parent
RAW_FILE = BASE_DIR / "data" / "raw" / "sales.csv"
PROCESSED_DIR = BASE_DIR / "data" / "processed"
OUTPUT_FILE = PROCESSED_DIR / "cleaned_sales.csv"


# STEP 1: EXTRACT
def extract_data(file_path):
    logging.info("Starting data extraction...")

    if not file_path.exists():
        raise FileNotFoundError(f"File not found: {file_path}")

    df = pd.read_csv(file_path)

    logging.info("Data extracted successfully.")
    logging.info("Total records: %s", len(df))

    return df


# STEP 2: TRANSFORM
def transform_data(df):
    logging.info("Starting data transformation...")

    # Remove duplicate records
    df = df.drop_duplicates()

    # Remove records with missing values
    df = df.dropna(subset=[
        "order_id",
        "product",
        "quantity",
        "price"
    ])

    # Convert data types
    df["quantity"] = pd.to_numeric(
        df["quantity"], errors="coerce"
    )
    df["price"] = pd.to_numeric(
        df["price"], errors="coerce"
    )

    df = df.dropna(subset=["quantity", "price"])

    # Calculate total sales
    df["total_sales"] = df["quantity"] * df["price"]

    logging.info("Data transformation completed.")

    return df


# STEP 3: LOAD
def load_data(df, output_file):
    logging.info("Starting data loading...")

    output_file.parent.mkdir(
        parents=True,
        exist_ok=True
    )

    df.to_csv(output_file, index=False)

    logging.info("Data loaded successfully: %s", output_file)


# MAIN PIPELINE
def main():
    try:
        logging.info("ETL PIPELINE STARTED")

        extracted_data = extract_data(RAW_FILE)

        transformed_data = transform_data(extracted_data)

        load_data(transformed_data, OUTPUT_FILE)

        logging.info("ETL PIPELINE COMPLETED SUCCESSFULLY")

    except Exception:
        logging.exception("ETL PIPELINE FAILED")
        raise


if __name__ == "__main__":
    main()
