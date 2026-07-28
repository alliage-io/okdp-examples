# Demo

OKDP-examples offers interactive Jupyter notebooks for exploring the OKDP platform with Trino and Spark.  

After launching the Applications service to load Q1 2025 NYC Taxi Parquet data into S3, you'll walk through a medallion architecture workflow:  

- Bronze: Perform initial exploratory data analysis (EDA).
- Silver: Clean the data and write it to a Polaris Iceberg table.
- Gold: Generate summary aggregations and build a pie/donut chart comparing Green vs. Yellow Taxi market share.  

## Jupyter Trino and Spark example notebooks

The Bronze level notebooks allow for some simple EDA on the raw NYC taxi data. The Silver level notebook does some lite cleaning of the raw data into a new table and the Gold level notebook creates a small aggregate table by month.   

1. In the control plane server open jupyter server
2. Select Sync OKDP examples
3. Navigate to the [Bronze level notebooks](https://jupyterhub-default.okdp.sandbox/user/adm/lab/tree/okdp-examples/notebooks/bronze/mobility/nyc_trip). These notebooks are independent and can be run in any order.
4. Navigate to [Silver level notebooks](https://jupyterhub-default.okdp.sandbox/user/adm/lab/tree/okdp-examples/notebooks/silver/mobility/nyc_trip) and run `01_build_trips.ipynb`. This will do light cleaning of the raw data in the bronze bucket. The table created with this notebook is used for building the "Gold" level table. **NOTE:** Do not run `01_Issue-seaweedFS-build_trips.ipynb`. It is strictly for trouble shooting a known issue in seaweedFS and will more than likely break the pipeline.
5. Navigate to [Gold level notebooks](https://jupyterhub-default.okdp.sandbox/user/adm/lab/tree/okdp-examples/notebooks/gold/mobility/nyc_trip) and run `01_build_monthly_stats.ipynb`. This builds a small monthly aggregate table that covers the first three months of 2025.  

## Add Superset Dataset with SQL query

Add the Gold table created with the Jupyter notebooks as a Dataset in Superset.  

1. In the control plane server, open the Superset UI.
2. Select `SQL -> SQL Lab` option near the top left of the UI.
3. Set the Database to `polaris-gold` and the Schema to `nyc_tlc`.
4. Enter and run the following SQL query:

```sql
SELECT * FROM monthly_stats
```

**Note:** Ensure there is not a trailing `;` as the query will not save correctly.  

5. Select the `Save -> Save dataset` option just above the query results on the right-hand side of the UI.

## Create simple Superset Chart

Create a simple Pie(Donut) chart to show the market share by total trips between Yellow and Green Taxis in NYC. 

1. Select `Charts` in the upper left side of the UI.
2. Select `+ Chart` option from the upper right hand corner.
3. Select `Gold Monthly NYC Taxi` as the `Choose a dataset` option and `Pie Chart` as the chart type.
4. Drag the `source_dataset` column to the Dimensions option in the Query. You may be prompted to authorize access to the dataset with keycloak. 
5. Drag `trip_count` to the Metrics and choose SUM as the aggregation.
6. Select Customize.
7. Set the Percentage threshold to 1.
8. Check the `Label Line`, `Show Total`, and `Donut` options and update chart.
