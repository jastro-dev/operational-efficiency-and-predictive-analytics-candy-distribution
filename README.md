### Operational Efficiency Benchmarking for Candy Distribution (Work in Progress)

This project aims to benchmark and improve the operational efficiency of a candy manufacturing and distribution business. This repository is a work in progress. It integrates diverse data sources, performs detailed analyses, and builds predictive and interactive tools to provide actionable insights and continuous monitoring for data-driven decision-making. Currently, a data pipeline is being built, exploratory data analysis has been performed, and the data has been prepped for predictive modeling and interactive dashboards.

---

## Project Structure

### **Data Pipeline**
1. **Data Integration**: Combines data from multiple CSV files (sales, production, factories, locations, and targets) using Pandas.
2. **Data Transformation**:
   - Cleans data including:
     - Handling missing numerical data by filling NaN values using Pandas `fillna` and `transform` with group means based on county and state.
     - Converts date columns (`Order Date`, `Ship Date`) to datetime objects using `pd.to_datetime`.
     - Removing duplicate records from all dataframes using `drop_duplicates`.
     - Renaming columns for consistency.
   - **Feature Engineering**:
     - Creates an `origin_postal_code` column based on the factory using a dictionary mapping.
     - Calculates delivery distances in kilometers using `pgeocode` and the Haversine formula (for international addresses).
     - Creates a `lead_time` column by subtracting `order_date` from `ship_date`.
   - Column dropping to remove redundancy
3. **Data Storage**: Stores cleaned data in an SQLite database using `pandas.to_sql`. Also implements a function to optionally store to a MySQL database, but is currently configured to skip insertion if the table exists.

### **Exploratory Data Analysis (EDA)**
- **Sales Trends**: Analyzes sales and profitability by division, product, and geography. Includes bar plots for gross profit and units by division and region. Visualizes top products by gross profit and units sold.
- **Production Efficiency**: Assesses factory performance and identifies the most profitable products and divisions.
- **Shipping Optimization**: Includes calculation of delivery distance using `pgeocode` and Haversine formula.
- **Target Gap Analysis**: Compares historical sales with 2024 targets to highlight potential risks (implemented in SQL).

### **Predictive Modeling**
-   *Planned*

### **Interactive Dashboards**
-   *Planned*

---

## Key Achievements
- Successfully combined data from sales, production, geographic, and target sources into a unified database.
- Implemented data cleaning and transformation steps, including handling missing values, standardizing formats, and converting data types.
- Engineered new features such as `origin_postal_code`, `distance_km`, and `lead_time` to enhance analysis and modeling.
- Performed initial EDA to identify key trends in sales performance, production efficiency, and supply chain management.
-  Setup database connections for both SQLite and MySQL, and implemented a test query for data validation.

---

## Installation and Setup

1.  **Clone the repository:**

    ```bash
    git clone https://github.com/jastro-dev/operational-efficiency-benchmarking-candy-distributor.git
    cd operational-efficiency-benchmarking-candy-distributor
    ```
2.  **Create a virtual environment (optional but recommended):**

    ```bash
    python3 -m venv venv
    source venv/bin/activate
    ```
3.  **Install dependencies:**

    ```bash
    pip install pandas numpy matplotlib seaborn pgeocode scikit-learn ipython-sql mysql-connector-python python-dotenv
    ```

4.  **Set up MySQL (optional):**
    - Create a `.env` file in the project root with the following variables:

    ```
    MYSQL_HOST=your_mysql_host
    MYSQL_USER=your_mysql_user
    MYSQL_PASSWORD=your_mysql_password
    MYSQL_NAME=your_mysql_database_name
    ```

    - Ensure MySQL is running and accessible.

5.  **Run the notebook:**

    ```bash
    jupyter notebook main_notebook.ipynb
    ```