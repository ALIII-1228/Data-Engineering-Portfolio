# 🎬 Netflix Data Engineering Pipeline

An end-to-end data engineering project built using **Databricks, PySpark, SQL, Delta Lake, and Power BI**.

## 📌 Project Overview & Pipeline

The goal of this project was to take a raw Netflix titles dataset and turn it into clean, reliable, and analytics-ready data that could be used to answer meaningful questions about Netflix's content.

I approached the project using the **Medallion Architecture**, where each layer has a different purpose:

```text
Raw Netflix Dataset
        │
        ▼
    🥉 Bronze
   Raw Data in Delta
        │
        ▼
    🥈 Silver
 Cleaning + Validation
 + Data Quality Tracking
        │
        ▼
     🥇 Gold
 Purpose-Built Analytical Tables
        │
        ▼
    📊 Power BI
 Interactive Dashboard
```

The **Bronze layer** preserves the raw Netflix dataset in Databricks as a Delta table. This provides an unmodified starting point for the rest of the pipeline.

The **Silver layer** focuses on cleaning, validating, standardizing, and enriching the data. Invalid and corrupted records are removed, data types are corrected, multi-purpose fields such as duration are separated into useful components, and missing values are tracked using data-quality flags and a 0–100 quality score.

The **Gold layer** takes the cleaned Silver data and creates six analytical tables, each designed around a specific question about Netflix's content. These tables are then used as the main data source for the Power BI dashboard.

---

## 🎯 Objective

The main objective of this project was to build a small but complete data pipeline that transforms raw Netflix content data into reliable, analytics-ready datasets.

Specifically, I wanted to:

* Identify and handle corrupted records
* Standardize inconsistent and improperly formatted values
* Convert fields into appropriate data types
* Preserve information about missing data
* Create a measurable data-quality score
* Transform the cleaned data into purpose-specific Gold tables
* Use those tables to answer meaningful analytical questions
* Visualize the results through Power BI

Rather than simply cleaning the dataset, the project focuses on making the transformation process **traceable, measurable, and useful for downstream analysis**.

---

# 🛠️ Technologies Used

| Technology                  | Purpose                                   |
| --------------------------- | ----------------------------------------- |
| **Databricks Free Edition** | Data engineering environment              |
| **PySpark**                 | Data cleaning and transformation          |
| **SQL**                     | Gold-layer analytical transformations     |
| **Delta Lake**              | Bronze and Silver table storage           |
| **Unity Catalog**           | Table organization and management         |
| **Power BI**                | Dashboard and visualization               |
| **GitHub**                  | Project documentation and version control |

---

# 🥉 Bronze Layer

The Bronze layer stores the raw Netflix dataset as a Delta table:

```text
data_engineering_projects.netflix_data.netflix_data_bronze
```

The purpose of this layer is to preserve the source data before applying transformations, providing a consistent starting point for the Silver layer.

---

# 🥈 Silver Layer

The Silver layer transforms the raw data into a cleaner and more analytics-ready dataset.

### Main transformations

* Removed **23 corrupted records**, retaining **8,786 records (99.74%)** of the source data.
* Validated fields such as `type` and `rating`.
* Converted `date_added` and `release_year` into appropriate data types.
* Split `duration` into `duration_value` and `duration_unit`.
* Standardized empty strings and whitespace.
* Renamed `listed_in` to `genres`.
* Added missing-value flags for `director`, `cast`, `country`, and `date_added`.
* Added a **0–100 `data_quality_score`** to measure record completeness.
* Added a `processed_timestamp` for processing traceability.

The final Silver table is:

```text
data_engineering_projects.netflix_data.netflix_data_silver
```

The main idea behind this layer was to **retain useful records while making their quality and completeness measurable**.

---

# 🥇 Gold Layer

Instead of creating one large analytical table, I created six Gold tables, each designed to answer a specific question.

### 📅 Content by Year

**`netflix_content_by_year_gold`**

**Answers:**
How does Netflix's content vary across release years, and how is it divided between Movies and TV Shows?

Includes total content, Movie/TV Show counts and percentages, quality scores, and date-added information.

---

### 🎬 Genre Analysis

**`netflix_genre_analysis_gold`**

**Answers:**
What genres are most represented on Netflix, and how are they distributed between Movies and TV Shows?

The multi-value genre field is split and exploded so individual genres can be analyzed independently. The table also includes content percentages, quality scores, and release-year statistics.

---

### 🌎 Country Production

**`netflix_country_production_gold`**

**Answers:**
Which countries are most represented in Netflix's catalog, and what type of content is associated with them?

The multi-value country field is split and exploded to support country-level analysis, including Movie/TV Show counts, content percentages, quality scores, and production-year statistics.

---

### 🔞 Rating Analysis

**`netflix_rating_analysis_gold`**

**Answers:**
What does the maturity profile of Netflix's content look like?

The table analyzes individual ratings and groups them into broader maturity categories such as **All Ages, Parental Guidance, Teens 13+, Mature 17+, and Not Rated**.

---

### 📈 Monthly Trends

**`netflix_monthly_trends_gold`**

**Answers:**
How does Netflix's content addition activity change over time?

The table tracks monthly content additions, Movies vs TV Shows, average quality scores, a **rolling 3-month average**, year-over-year comparisons, and the number of distinct ratings added.

---

### ✅ Content Quality Metrics

**`netflix_content_quality_metrics_gold`**

**Answers:**
How complete and reliable is the processed Netflix dataset?

This table summarizes record counts, Movie/TV Show distribution, field completeness, missing-value counts, quality-score statistics, quality tiers, and overall data coverage.

---

# 📊 Power BI Dashboard

The Gold layer serves as the main source for the Power BI dashboard.

The dashboard brings the different analytical perspectives together and focuses on:

### Content Overview

* Total titles
* Movies vs TV Shows
* Content by release year

### Content Distribution

* Top genres
* Top countries
* Rating distribution
* Maturity categories

### Content Trends

* Monthly content additions
* Movies vs TV Shows added over time
* Rolling 3-month trends
* Year-over-year comparisons

### Data Quality

* Average quality score
* Quality tiers
* Field completeness
* Missing-value counts

The purpose of the dashboard is to provide an interactive way of consuming the analytical datasets produced by the Databricks pipeline.

---

# 📈 Data Quality Results

One of the main goals of the project was to make data quality measurable rather than simply assuming the cleaned data was reliable.

| Metric                     | Result |
| -------------------------- | -----: |
| Records processed          |  8,809 |
| Records retained           |  8,786 |
| Records filtered           |     23 |
| Retention rate             | 99.74% |
| Perfect-quality records    |  5,317 |
| Perfect-quality percentage | 60.52% |

The 23 filtered records were removed because they contained corrupted or invalid values rather than simply because they had missing attributes.

---

# 🔄 End-to-End Workflow

```text
                 Netflix CSV
                     │
                     ▼
              ┌─────────────┐
              │   BRONZE    │
              │             │
              │  Raw Data   │
              └──────┬──────┘
                     │
                     ▼
              ┌─────────────┐
              │   SILVER    │
              │             │
              │ • Cleaning  │
              │ • Validation│
              │ • Standard. │
              │ • Data Qual.│
              └──────┬──────┘
                     │
                     ▼
        ┌────────────────────────────┐
        │            GOLD            │
        │                            │
        │ • Content by Year          │
        │ • Genre Analysis            │
        │ • Country Production        │
        │ • Rating Analysis           │
        │ • Monthly Trends            │
        │ • Quality Metrics           │
        └──────────────┬─────────────┘
                       │
                       ▼
                ┌────────────┐
                │  Power BI  │
                │ Dashboard  │
                └────────────┘
```

---

# 🚀 Future Improvements

If I continue developing this project, I would like to explore:

* Automating the ingestion process
* Implementing incremental data loading
* Adding more automated data-quality checks
* Enriching the dataset with additional sources
* Improving the Power BI data model
* Adding more advanced analytics
* Deploying the pipeline in a cloud-based production environment

---

# 👤 About the Project

This project was created as a hands-on Data Engineering portfolio project to strengthen my experience with **Databricks, PySpark, SQL, Delta Lake, data quality, and analytical data modeling**.

The main idea was to demonstrate the complete flow of turning **raw data into cleaned data, analytical datasets, and finally a business-facing dashboard**.

---

# 📚 Dataset

The project uses the **Netflix Movies and TV Shows** dataset containing information about titles available on Netflix, including their type, title, director, cast, country, date added, release year, rating, duration, genres, and description.
