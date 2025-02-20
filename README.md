# Operational Efficiency Benchmarking for Candy Distribution (Work in Progress)

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

-   **Objective**: Predict late shipments using classification models. The target variable, `late`, is a binary indicator derived from `ship_mode` and `lead_time`, where 1 indicates a late shipment and 0 indicates on-time delivery.

-   **Models Implemented**:
    -   Logistic Regression
    -   Decision Tree
    -   Random Forest (Standard and Out-of-Bag (OOB) variants)
    -   XGBoost

-   **Feature Engineering and Selection**:
    - Engineered features such as `origin_postal_code`, `distance_km`, and `lead_time` (described in the Data Pipeline section) were used.
    - Redundant columns (`lead_time`, `ship_quarter`, `ship_month`, `ship_day`, `ship_year`, `order_quarter`, `order_month`, `order_day`, `order_year`, `target`) were removed to prevent overfitting and improve model focus.
    - Sequential Feature Selection was employed to identify the most relevant features. The top 5 features identified were: `ship_mode_Same Day`, `ship_mode_Standard Class`, `division_Other`, `region_Interior`, and `unit_cost`.

-   **Evaluation Metrics**:
    - ROC AUC (Area Under the Receiver Operating Characteristic Curve)
    - Confusion Matrix
    - Classification Report (Precision, Recall, F1-Score)

-   **Key Findings**:
    - The implemented models achieved relatively similar performance, with ROC AUC scores ranging from 0.56 to 0.61 on the test data.
    - Feature selection with the top 5 features resulted in minor performance changes. The Logistic Regression model showed a slight improvement and achieved the highest ROC AUC score of 0.6085 with selected features.
    - Overall the selected features achieved similar, or slightly better, generalization than models run on the entire dataset.

-   **Results Summary**:

    | Model                     | ROC AUC | Confusion Matrix           | Features   |
    |---------------------------|---------|----------------------------|------------|
    | Logistic Regression       | 0.6058  | \[[1029, 1004], [602, 1442]] | All        |
    | Decision Tree             | 0.6063  | \[[884, 1149], [454, 1590]]  | All        |
    | Random Forest Standard    | 0.5637  | \[[1160, 873], [906, 1138]]   | All        |
    | Random Forest OOB         | 0.5713  | \[[2897, 2194], [2176, 2927]] | All        |
    | XGBoost                   | 0.6011  | \[[1005, 1028], [597, 1447]] | All        |
    | Logistic Regression       | 0.6085  | \[[1028, 1005], [590, 1454]]  | Selected   |
    | Decision Tree             | 0.6071  | \[[909, 1124], [476, 1568]]   | Selected   |
    | Random Forest Standard    | 0.6041  | \[[994, 1039], [574, 1470]]   | Selected   |
    | Random Forest OOB         | 0.6079  | \[[2479, 2612], [1384, 3719]]  | Selected   |
    | XGBoost                   | 0.6059  | \[[917, 1116], [489, 1555]]   | Selected   |

-   **Next Steps**: Further investigation is needed to improve model performance. This could involve exploring different modeling techniques, further feature engineering, or hyperparameter tuning.


### **Interactive Dashboards**
-   The culmination of this project is an interactive dashboard providing a comprehensive view of operational efficiency. Data from the MySQL database was pulled and transformed to create key performance indicators (KPIs) and visualizations.
-   **Key Features:**
    -   **Quarterly KPI Performance:** Revenue against targets per division, enabling quick assessment of goal attainment. For example, Chocolate Quarterly Sales are at $16.41K against a goal of $27K (-39.22%), Sugar Quarterly Sales are at $40.93K against a goal of $15K (-99.73%) and Other Quarterly Sales are at $1.51K against a goal of $3K (-49.54%).
    -   **Lead Time vs. Late Probability Analysis:** A scatter plot visualizing the relationship between product lead times and the probability of late delivery, with bubble size indicating unit profit. This allows for targeted efforts towards products needing delivery improvements. Key products identified for optimization are detailed below:

        | Product              | Average Predicted Probability of Lateness | Median Lead Time |
        |----------------------|-------------------------------------------|------------------|
        | Fizzy Lifting Drinks | 79.23%                                    | 6 days           |
        | Nerds                | 61.57%                                    | 6 days           |
        | Laffy Taffy          | 58.16%                                    | 6 days           |

    -   **Total and Monthly Revenue Analysis:** Filterable by division, factory, product name, region, and ship mode, providing granular insights into revenue drivers and trends. The Total Revenue bar graph and Monthly Average Revenue line graph are included.
-   **Important Limitation:** Due to the data having a cutoff at the end of 2024, Year-to-Date (YTD) measures were created using alternative methods. The current date was interpreted as December 1, 2024, and data was filtered based on relevant variables instead of using standard time intelligence functions.
-   This dashboard serves as a central hub for continuous monitoring and data-driven decision-making, completing the end-to-end operational efficiency benchmarking solution.

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
