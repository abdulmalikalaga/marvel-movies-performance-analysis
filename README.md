# 📊 Marvel Movie Data Analytics Power BI Dashboard

# 🧠 Project Overview

## Objective
To build a complete analytics dashboard exploring the financial performance and audience reception of MCU (Marvel Cinematic Universe) and Non-MCU Marvel films.  

The goal was to compare revenue, profitability, and critical reception across universes, phases, and individual movies.

## Context
Marvel movies dominate global cinema, but their financial and audience performance varies widely.  

This project answers key questions such as:

- Do MCU movies earn more than Non-MCU movies?
- How aligned are critics and audiences in their ratings?
- Which movies produced the best profit and ROI?
- Which directors, phases, or universes generated the strongest financial performance?

The dashboard serves as a data-driven exploration of the Marvel franchise’s cinematic and financial landscape.

---

# 🗂️ Data Overview

## Source
Data was collected from multiple Kaggle datasets:

- https://www.kaggle.com/datasets/sarthakbharad/marvel-movies-dataset
- https://www.kaggle.com/datasets/davidgdong/marvel-cinematic-universe-box-office-dataset
- https://www.kaggle.com/datasets/dalepeh/mcu-movies-dataset
- https://www.kaggle.com/datasets/monkeybusiness7/marvel-cinematic-universe-box-office
- https://www.kaggle.com/datasets/aakashsudhakar/mcu-ratings-and-box-office-data-updated-2025
- https://www.kaggle.com/datasets/darrylljk/marvel-cinematic-universe-films-box-office/data

## Description
The combined datasets included:

- Movie names and release dates
- Box office data: domestic, international, worldwide
- Budgets
- IMDb ratings
- Rotten Tomatoes critic & audience scores
- CinemaScore
- Letterboxd ratings
- MCU phases
- Studios (Disney, Fox, Sony)

## Preparation in Power BI / Power Query

### Key data preparation steps:
- Standardised column names across all datasets
- Cleaned inconsistent movie titles
- Removed duplicates
- Changed all currency columns to numeric types
- Built a single `Movie ID` key to avoid issues from spelling variations
- Performed multiple left-joins using Power Query merge
- Created calculated columns using M language

### Example M Transformation
```m
= if [Movie ID] = 69 and [International Box Office] = null 
  then [Worldwide Box Office] - [Domestic Box Office] 
  else [International Box Office]
```
<img width="774" height="495" alt="m transformation" src="https://github.com/user-attachments/assets/ec36e2ae-c8bf-4658-ac50-260aea3d0924" />

---

# 🧮 Data Modelling

## 📎  Model Structure
A clean **star schema** was built.

### 1. Fact Tables
- `FactBoxOffice`
- `FactRatings`

### 2. Dimension Tables
- `DimMovies`
- `DimUniverse`
- `DimDate`

## 🔑  Key Relationships
- `MovieID` (DimMovies → FactTables)
- `UniverseID` (DimUniverse → DimMovies)
- `DateID` (DimDate → DimMovies or FactBoxOffice)

<img width="1436" height="808" alt="data model screenshot" src="https://github.com/user-attachments/assets/a82d9b66-ea18-46cf-964a-cba0c96437e6" />

## 📏  Some Added Measures

### Financial Measures
```DAX
Total Worldwide Box Office = SUM(FactBoxOffice[Worldwide Box Office])

Total Domestic Box Office = SUM(FactBoxOffice[Domestic Box Office])

Total International Box Office = SUM(FactBoxOffice[International Box Office])

Total Budget = SUM(FactBoxOffice[Budget])

Profit = [Total Worldwide Box Office] - [Total Budget]

ROI % = DIVIDE([Profit], [Total Budget])
```

### Ratings Measures
```DAX
Average IMDb Score = AVERAGE(FactRatings[IMDb Rating])

Average Critics Score = AVERAGE(FactRatings[Critics Score])

Average Audience Score = AVERAGE(FactRatings[Audience Score])
```

### Top N Logic
```DAX
Top 5 Worldwide =
IF(
    RANKX(
        ALLSELECTED(DimMovies[Movie]),
        [Total Worldwide Box Office],
        ,
        DESC
    ) <= 5,
    [Total Worldwide Box Office]
)
```
### Additional Transformations
- Built the Universe dimension table (MCU vs Non-MCU) using conditional logic
- Fixed relationship issues that caused identical average IMDb scores in both universes

### ⚠️ Important Fix ⚠️
Initially, MCU and Non-MCU showed the same IMDb average due to a relationship misalignment.  

Correcting the `DimUniverse → DimMovies` and `DimMovies → FactRatings` relationships resolved the issue.

---

# 📈 Dashboard Design

## Purpose
To create an interactive analytic suite enabling:

- Financial comparisons
- Ratings breakdowns
- Universe-level vs movie-level insights
- Deep-dive analysis of box office, profit, ratings, and ROI

## Design Features
- Marvel-themed colour palette (`#950f03` + custom blue/red tones)
- Clean title cards across all pages
- Precise grid-based layout
- Page-level navigation through tabs
- Use of cards, bar charts, scatter plots, donut charts, and matrix visuals
- Conditional formatting for Profit, ROI %, and Matrix rating alignment
- Slicer panels for Universe, Phase, Year, Director, and Movie

---

## Page Breakdown

### Page 1 — Overview
- KPI cards
- Scatter: Ratings vs Worldwide Box Office
- Top 5 highest-grossing movies
- Universe-level comparison visual

### Page 2 — Financial Analysis
- Profit by Movie
- ROI % by Movie
- Domestic vs International contribution

### Page 3 — More Financial Analysis
- Worldwide Box Office by MCU Phase
- Profit by Universe
- ROI % by Universe
- Slicers for Year, Director, Universe, and Phase

### Page 4 — Ratings & Reception
- KPIs for ratings
- Scatter: Ratings vs Worldwide
- Universe comparison of average ratings
- Searchable movie matrix for critic vs audience alignment

## 🖼️ Dashboard Preview:

<img width="1436" height="808" alt="marvel dashboard viz" src="https://github.com/user-attachments/assets/9aa2ae72-c4bf-4ac5-acac-23403b0cd4f1" />

---

## Interactivity
- Movie slicer controlling only the matrix
- Edit Interactions to prevent slicers controlling irrelevant visuals
- Conditional formatting highlighting highest and lowest ROI
- Filters restricting top 5 visuals
- Page slicers for deeper exploration of directors, phases, universes, and years

---

# 🔍 Insights

## Key Findings

### 1. MCU significantly outperforms Non-MCU financially
Even with roughly equal numbers of movies containing complete worldwide box office data, MCU films generated substantially higher total revenue.

### 2. Critics and audiences align more than expected
Most movies showed similar trends across IMDb, Rotten Tomatoes Critic, and Rotten Tomatoes Audience scores.

### 3. Director profitability varies across universes
For example, Sam Raimi generated significantly higher profit from Non-MCU films compared to his MCU entries.

### 4. International markets dominate revenue
Many movies earned over 60–70% of total revenue internationally.

### 5. ROI varies dramatically
Some movies achieved ROI figures exceeding 300–1200%, demonstrating how relatively small budgets can generate massive returns.

---

# ⚙️ Challenges, Causes and Solutions

## ⚠️ Inconsistent movie titles across datasets
### ✅ Solution
Created a universal `MovieID` and rebuilt merges using IDs rather than titles.

---

## ⚠️ Universe averages appearing identical
### ❓Cause
Relationship misconfiguration.

### ✅ Solution
Corrected:
- `DimUniverse → DimMovies`
- `DimMovies → FactRatings`

---

## ⚠️ Missing International Box Office values
### ✅ Solution
Used an M-language conditional transformation to calculate:

```m
International = Worldwide - Domestic
```

---

## ⚠️ Ratings tables containing null values
### ✅ Solution
Designed measures and visuals that ignore blanks gracefully.

---

## ⚠️ Dashboard overcrowding
### ✅ Solution
Introduced:
- strict spacing rules
- consistent visual sizing
- page-specific layouts
- aligned grid structure

---

# 🚀 Outcome / Value

This dashboard enables users to:

- Compare MCU vs Non-MCU performance
- Analyse revenue composition
- Evaluate movie-level ROI and profitability
- Examine critic vs audience alignment
- Explore director-specific performance
- Investigate MCU phases
- Drill into individual movie performance using slicers

The final result is a polished, interactive Power BI dashboard demonstrating strong use of:

- Data modelling
- Power Query transformations
- DAX
- Visual storytelling
- Dashboard UI/UX principles

# 📁 PBIX File

[Download the Power BI Dashboard](https://github.com/abdulmalikalaga/marvel-movies-performance-data-analysis/blob/main/pbix/marvel%20movies%20performance.pbix)

---

⭐ **If you found this project insightful, please star ⭐ this repository!**  
📬 *Let’s connect on [LinkedIn](https://www.linkedin.com/in/abdulmalikalaga/)
 — open to data-based analytics roles.

