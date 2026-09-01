# Bangkok Public Issue Report Analysis

## NLP & Data Analysis of Bangkok Public Issue Reports

## Project Overview

This project analyzes public issue reports in Bangkok to identify common urban problems, compare issue patterns across districts, and explore recurring topics in Thai-language comments.

The main objective is to transform unstructured public complaint data into **actionable insights that can support urban operations, issue prioritization, and resource allocation**.

The project combines:

* Exploratory Data Analysis (EDA)
* Thai Natural Language Processing (NLP)
* Rule-based text classification
* District-level issue analysis
* TF-IDF text representation
* Unsupervised topic clustering using MiniBatch K-Means

---

## Business Problem

City authorities receive a large number of public issue reports covering different types of urban problems.

However, raw complaint data alone does not clearly indicate:

* Which problems occur most frequently?
* Which districts receive the most reports?
* Which issues are particularly concentrated within specific districts?
* What recurring topics appear in Thai-language comments?
* How should operational teams prioritize problems and allocate resources?

This project uses data analysis and NLP techniques to convert public reports into structured information that can support these decisions.

---

## Analysis Workflow

`Data Collection → Data Validation → Data Cleaning → Exploratory Data Analysis → Thai Text Classification → District Analysis → Topic Discovery → Operational Insights`

### 1. Data Collection & Validation

The public issue-report dataset was loaded and inspected for:

* Data types
* Missing values
* Duplicate records
* District information
* Issue descriptions
* Thai-language comments
* Report status and timestamps

The analysis contains **20,730 reports**.

---

### 2. Exploratory Data Analysis

Exploratory analysis was performed to understand the distribution of reports across Bangkok.

The analysis examines:

* Number of reports by district
* Distribution of issue categories
* Report volume
* Issue concentration within districts

This provides an initial view of where public issues are being reported and which problems occur most frequently.

---

## Thai Text Classification

Thai-language comments were classified into practical urban issue categories using transparent keyword-based rules.

The classification identifies categories such as:

* Sidewalk problems
* Traffic
* Garbage / odor / wastewater
* Flooding / drainage
* Safety
* Lighting
* Other urban infrastructure issues

The rule-based classification successfully identified predefined issue categories in approximately **64.46% of reports**.

Using transparent rules also makes it possible to understand why a report was assigned to a particular category.

---

## Key Findings

### 1. Sidewalk Issues Were the Most Frequently Identified Problem

**Sidewalk-related issues (ทางเท้า)** were the most frequently identified category, representing approximately **20.99% of reports**.

This suggests that pedestrian infrastructure represents an important area for operational attention.

---

### 2. Chatuchak Had the Highest Reporting Volume

**Chatuchak (จตุจักร)** recorded the largest overall reporting volume with approximately **1,462 reports**.

High report volume can indicate a larger operational workload and may help identify districts requiring additional attention or resources.

---

### 3. Pom Prap Sattru Phai Had a High Concentration of Sidewalk Issues

Among eligible districts, **Pom Prap Sattru Phai (ป้อมปราบศัตรูพ่าย)** had the highest rate of sidewalk-related reports at approximately **42.58%**.

This demonstrates why analyzing only the total number of complaints may not be sufficient.

A district may not have the largest overall report volume but may still have a strong concentration of a specific local problem.

---

## Volume vs. Issue Rate

Two perspectives were used when comparing districts:

**Issue Volume**
The number of reports related to a particular issue.

**Issue Rate**
The percentage of all reports within a district that mention that issue.

Using both metrics provides a more balanced view of urban problems.

For example:

* High volume can indicate a large operational workload.
* High issue rate can indicate a concentrated local problem.

Districts with very few reports were excluded from rate rankings to reduce unstable comparisons.

---

## Thai NLP & Word Analysis

Thai text processing was performed using **PyThaiNLP**.

Comments were tokenized and analyzed to identify frequently occurring words within selected issue categories.

A WordCloud was also used to visualize recurring terms in Thai-language public reports.

This helps provide additional context beyond structured issue categories.

---

## Unsupervised Topic Discovery

Rule-based classification can only identify issues that have already been defined in the keyword dictionary.

To discover additional patterns, an unsupervised NLP approach was also applied.

Thai comments were converted into **TF-IDF character n-gram features** and grouped using **MiniBatch K-Means clustering**.

Character n-grams were selected because they can capture useful Thai-language patterns without depending entirely on word-boundary detection.

The resulting TF-IDF matrix contained:

**20,730 reports × 12,000 text features**

The analysis grouped the comments into **15 exploratory topic clusters**.

Representative character patterns and sample reports were then inspected to understand the themes captured by each cluster.

---

## Why Use Both Rule-Based Classification and Clustering?

The two approaches serve different purposes.

### Rule-Based Classification

Useful for:

* Known business categories
* Transparent classification
* Operational reporting
* Easy interpretation

### Unsupervised Clustering

Useful for:

* Discovering unknown patterns
* Exploring recurring themes
* Identifying topics outside predefined categories

Combining both approaches provides a more flexible framework for analyzing public issue reports.

---

## Business & Operational Recommendations

### Prioritize Using Both Volume and Rate

Operational teams should consider both the absolute number of reports and the concentration of issues within each district.

This prevents smaller but highly concentrated local problems from being overlooked.

### Route Issues to Responsible Teams

Issue categories can be mapped to responsible operational teams such as:

* Road and sidewalk maintenance
* Drainage
* Waste management
* Traffic management
* Public safety
* Lighting and infrastructure

This can support faster issue routing and operational planning.

### Monitor District-Level Patterns

District-level monitoring can help identify areas where specific problems repeatedly occur.

This information can support targeted inspections and resource allocation.

### Monitor Emerging Topics

Unsupervised topic clustering can complement predefined categories by identifying recurring themes that may not yet exist in the classification rules.

---

## Technologies Used

* Python
* Pandas
* NumPy
* Matplotlib
* Seaborn
* PyThaiNLP
* Scikit-learn
* TF-IDF
* MiniBatch K-Means
* WordCloud
* Google Colab / Jupyter Notebook

---

## Limitations

This analysis should be interpreted with several limitations in mind.

* Keyword-based classification depends on the quality and coverage of the predefined keyword dictionary.
* Approximately 35% of reports were not captured by the predefined rule-based issue categories.
* Complaint volume does not necessarily represent the true frequency of problems because reporting behavior may differ across districts.
* Topic clusters generated through unsupervised learning require human interpretation.
* Text clustering identifies linguistic similarity and should not automatically be treated as confirmed operational categories.

---

## Future Improvements

Potential improvements include:

* Expanding and validating the Thai issue keyword dictionary
* Developing supervised NLP classification models
* Evaluating classification accuracy using manually labeled reports
* Adding geographic visualization and issue heatmaps
* Analyzing issue trends over time
* Monitoring unresolved-report aging by district
* Developing an interactive dashboard for operational monitoring
* Using topic discovery to identify emerging issue categories

---

## Conclusion

This project demonstrates how **data analysis and Natural Language Processing can transform unstructured public complaint data into actionable information**.

By combining exploratory analysis, transparent Thai text classification, district-level comparisons, and unsupervised topic discovery, the analysis provides multiple perspectives on urban public issues.

The overall analytical process can be summarized as:

**Public Reports → Data Cleaning → Thai NLP → Issue Classification → District Analysis → Topic Discovery → Operational Insights**

The results demonstrate how data can support more systematic **problem prioritization, resource allocation, and operational decision-making**.

---

# 日本語概要

## バンコク市民問題レポート分析

本プロジェクトでは、バンコクで報告された市民からの問題・苦情データを対象に、**データ分析およびタイ語の自然言語処理（NLP）**を行いました。

主な目的は、市民から寄せられた非構造化データを分析し、都市問題の優先順位付けや行政リソースの配分に活用できる情報へ変換することです。

分析では、

* データクレンジング・EDA
* 地区別レポート分析
* タイ語テキスト処理
* キーワードベースの問題分類
* TF-IDFによるテキスト特徴量化
* MiniBatch K-Meansによるトピッククラスタリング

を実施しました。

分析の結果、**歩道（ทางเท้า）に関する問題が約20.99%と最も多く**、また地区ごとに問題の件数だけでなく、特定の問題が占める割合にも大きな違いがあることが確認できました。

さらに、既存のキーワード分類だけでは把握できないテーマを探索するため、TF-IDFとクラスタリングを使用してタイ語コメントから潜在的なトピックを分析しました。

本プロジェクトを通して、

**データ収集 → データ分析 → NLP → 問題分類 → 地区比較 → 課題発見 → 意思決定支援**

という一連のデータ分析プロセスを実践しました。

