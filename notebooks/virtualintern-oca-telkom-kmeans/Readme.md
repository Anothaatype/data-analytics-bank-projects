# FM Clustering: Frequency-Monetary Customer Segmentation

## Overview

The `FMClustering` class implements a customer segmentation system based on **Frequency** and **Monetary** analysis. Unlike traditional RFM (Recency, Frequency, Monetary) analysis, this implementation focuses purely on transaction frequency and monetary value for clustering, using recency only for descriptive insights.

## Key Concepts

### What is FM Clustering?
- **Frequency (F)**: How often a customer makes transactions
- **Monetary (M)**: How much money a customer spends in total
- **Clustering**: Grouping customers with similar F-M patterns into strategic segments

### Why FM Instead of RFM?
The code emphasizes that clustering is performed **only** on Frequency and Monetary values because:
- These two dimensions capture the most important customer behaviors for segmentation
- Recency is used descriptively but doesn't drive the clustering algorithm
- This approach creates cleaner, more actionable customer segments

## Class Architecture

### Initialization
```python
clustering = FMClustering(df)
```
**Required DataFrame columns:**
- `user_id`: Unique customer identifier
- `transaction_id`: Unique transaction identifier  
- `total_price`: Transaction monetary value
- `created_at`: Transaction timestamp
- `user_name`, `channel_type`, `message_status`, `field_of_business`: Additional descriptive fields

### Core Attributes
- `df`: Original transaction data
- `df_cleaned`: Preprocessed transaction data
- `fm_data`: Customer-level FM metrics
- `clustered_data`: Final results with segment assignments
- `optimal_k`: Recommended number of clusters
- `scaler`: StandardScaler for feature normalization

## Method Breakdown

### 1. `preprocess_data()`
**Purpose**: Transform transaction-level data into customer-level FM metrics

**Process**:
1. **Data Validation**: Checks for required columns and handles missing values
2. **DateTime Processing**: Converts timestamps and removes invalid dates
3. **FM Calculation**: Aggregates transactions by customer:
   - **Frequency**: Count of transactions per customer
   - **Monetary**: Sum of total_price per customer
   - **Recency**: Days since last transaction (descriptive only)
4. **Feature Engineering**: Creates additional metrics like average transaction value

**Key Output**: `fm_data` DataFrame with customer-level metrics

**Important Note**: Only Frequency and Monetary are used for clustering; recency is purely descriptive.

### 2. `find_optimal_clusters(max_k=10)`
**Purpose**: Determine the best number of clusters using statistical methods

**Methods Used**:
- **Elbow Method**: Plots Within-Cluster Sum of Squares (WCSS) to find the "elbow" point
- **Silhouette Analysis**: Measures how well-separated clusters are

**Process**:
1. Scales FM features using StandardScaler
2. Tests different k values (2 to max_k)
3. Calculates WCSS and silhouette scores for each k
4. Creates dual visualization plots
5. Recommends optimal k based on highest silhouette score

**Output**: Returns optimal k value and creates visualization plots

### 3. `perform_clustering(n_clusters=3)`
**Purpose**: Execute K-means clustering and assign segment names

**Process**:
1. **Feature Selection**: Uses only Frequency and Monetary columns
2. **Data Scaling**: Applies StandardScaler transformation
3. **K-means Clustering**: Performs clustering with specified number of clusters
4. **Segment Naming**: Assigns meaningful names based on cluster characteristics:
   - Ranks clusters by combined F+M score
   - For 3 clusters: "Cluster_1_High_Value", "Cluster_2_Medium_Value", "Cluster_3_Low_Value"
5. **Results Summary**: Prints detailed cluster characteristics

**Key Features**:
- Handles missing data gracefully
- Provides statistical summary of each cluster
- Uses consistent naming convention based on performance ranking

### 4. `validate_segmentation()`
**Purpose**: Verify cluster quality through statistical visualization

**Creates Four Boxplot Analyses**:
1. **Frequency by Segment** (Primary clustering feature)
2. **Monetary by Segment** (Primary clustering feature)  
3. **Recency by Segment** (Descriptive only)
4. **Average Transaction Value by Segment** (Descriptive)

**Validation Criteria**:
- Minimal overlap between clusters in F-M space
- Clear separation in boxplot distributions
- Statistical summaries showing distinct segment characteristics

### 5. `create_fm_quadrant_visualization()`
**Purpose**: Create intuitive scatter plot showing FM relationships

**Components**:
1. **Scatter Plot**: Frequency vs Monetary with cluster colors
2. **Quadrant Labels**: Based on median thresholds:
   - **Champions**: High F, High M
   - **Big Spenders**: Low F, High M  
   - **Loyal Customers**: High F, Low M
   - **Potential Loyalists**: Low F, Low M
3. **Pie Chart**: Shows segment distribution
4. **Summary Statistics**: Prints quadrant analysis

### 6. `analyze_channel_distribution()`
**Purpose**: Understand channel preferences by FM segment

**Analysis**:
- Cross-tabulation of segments vs channels
- Percentage distribution heatmap
- Stacked bar chart showing channel usage
- Identifies which segments prefer which channels

### 7. `analyze_time_distribution()`
**Purpose**: Examine temporal patterns by FM segment

**Time Dimensions Analyzed**:
- **Hourly patterns**: Transaction distribution by hour of day
- **Weekly patterns**: Transaction distribution by day of week
- **Monthly patterns**: Transaction and revenue distribution by month

**Insights Generated**:
- Peak activity times for each segment
- Seasonal trends in transaction behavior
- Revenue concentration patterns

### 8. `generate_strategic_recommendations()`
**Purpose**: Provide actionable business strategies based on FM profiles

**Strategy Framework**:
Each segment receives targeted recommendations based on FM characteristics:

- **High F + High M (Champions)**: Focus on retention and VIP treatment
- **Low F + High M (Big Spenders)**: Increase transaction frequency  
- **High F + Low M (Loyal Customers)**: Increase spend per transaction
- **Low F + Low M (Potential Loyalists)**: Activation and development

**Financial Impact Projections**:
- Calculates potential revenue increases
- Estimates ROI for different strategies
- Provides concrete dollar impact figures

### 9. `generate_cluster_recommendations()`
**Purpose**: Alternative recommendation system without predefined segment names

**Approach**:
- Analyzes cluster characteristics relative to dataset medians
- Generates strategies based on actual FM performance
- More flexible for datasets that don't fit standard segment patterns

### 10. `save_results(output_file=None)`
**Purpose**: Export comprehensive analysis results

**Files Created**:
1. **Customer-level segments**: Main FM segmentation results
2. **Transaction-level data**: All transactions with segment assignments
3. **Statistical summary**: Aggregate statistics by segment

## Usage Example

```python
# Initialize the clustering system
clustering = FMClustering(df)

# Step 1: Create FM metrics
clustering.preprocess_data()

# Step 2: Find optimal number of clusters  
clustering.find_optimal_clusters()

# Step 3: Perform clustering (uses optimal k or default 3)
clustering.perform_clustering(n_clusters=3)

# Step 4: Validate results
clustering.validate_segmentation()

# Step 5: Visualize FM quadrants
clustering.create_fm_quadrant_visualization()

# Step 6: Analyze additional dimensions
clustering.analyze_channel_distribution()
clustering.analyze_time_distribution()

# Step 7: Get strategic recommendations
clustering.generate_strategic_recommendations()

# Step 8: Save all results
clustering.save_results()
```

## Key Technical Features

### Data Handling
- **Missing Value Management**: Gracefully handles incomplete data
- **Data Type Conversion**: Automatic datetime parsing and validation
- **Outlier Handling**: Uses scaled features for robust clustering

### Statistical Methods
- **K-means Clustering**: Primary segmentation algorithm
- **Silhouette Analysis**: Cluster quality measurement
- **StandardScaler**: Feature normalization for fair clustering

### Visualization
- **Matplotlib/Seaborn Integration**: Professional statistical plots
- **Multi-panel Layouts**: Comprehensive visual analysis
- **Color-coded Segments**: Consistent visual identity across plots

## Business Value

### Strategic Insights
1. **Customer Prioritization**: Identify high-value customers for retention
2. **Marketing Optimization**: Tailor campaigns to segment behaviors
3. **Revenue Growth**: Specific strategies to increase customer value
4. **Resource Allocation**: Focus efforts on highest-impact opportunities

### Measurable Outcomes
- **Retention Improvement**: Targeted strategies for valuable customers
- **Frequency Increase**: Convert occasional buyers to regular customers  
- **Spend Optimization**: Increase transaction values through upselling
- **Customer Development**: Activate low-performing segments

## Important Design Decisions

### Why Only F-M for Clustering?
The code explicitly uses only Frequency and Monetary for clustering because:
- These capture the most fundamental customer behaviors
- Recency can be misleading for segmentation (recent customers might be one-time buyers)
- Simpler feature space leads to more interpretable and actionable segments

### Cluster Naming Strategy
- Names are assigned based on actual performance rather than assumptions
- Rankings use combined F+M scores for objective prioritization
- Flexible naming accommodates different numbers of clusters

### Validation Approach
- Multiple validation methods ensure robust segmentation
- Both statistical and visual validation techniques
- Descriptive analysis includes recency for complete customer understanding

This implementation provides a complete, production-ready customer segmentation system with built-in validation, visualization, and strategic recommendations.