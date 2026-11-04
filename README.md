
import pandas as pd

# Step 1: Extract data from CSV file
def extract_data(file_path):
    try:
        df = pd.read_csv(file_path)

        print("Data extracted successfully!")
        print(f"Total Rows: {len(df)}")
        print(f"Total Columns: {len(df.columns)}")

        return df

    except FileNotFoundError:
        print("Error: File not found.")
        return None

# Step 2: Execute extraction
if __name__ == "__main__":
    file_path = "employees.csv"

    extracted_data = extract_data(file_path)

    if extracted_data is not None:
        print("\nExtracted Data:")
        print(extracted_data.head())
        # Data-Engineering-Portfolio
My Data Engineering and Analytics Projects
