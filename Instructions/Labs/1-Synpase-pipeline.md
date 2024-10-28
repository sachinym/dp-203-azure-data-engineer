# Lab 1: Build a data pipeline in Azure Synapse Analytics

## Lab Scenario

In this lab, you'll load data into a dedicated SQL Pool using a pipeline in Azure Synapse Analytics Explorer. The pipeline will encapsulate a data flow that loads product data into a table in a data warehouse.

### Objectives
  
In this lab, you will be able to complete the following tasks:

- Task 1: Provision an Azure Synapse Analytics workspace
- Task 2: View source and destination data stores
- Task 3: Implement a pipeline
- Task 4: Debug the Data Flow
- Task 5: Publish and run the pipeline

### Estimated timing: 45 minutes

### Architecture Diagram

   ![Azure portal with a cloud shell pane](./Lab-Scenario-Preview/media/lab1dp.png)

## Task 1: Provision an Azure Synapse Analytics workspace

You'll need an Azure Synapse Analytics workspace with access to data lake storage and a dedicated SQL pool hosting a relational data warehouse.

In this task, you'll use a combination of a PowerShell script and an ARM template to provision an Azure Synapse Analytics workspace.

1. Use the **[\>_]** button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, and select ***PowerShell*** environment.
    
    ![Azure portal with a cloud shell pane](./images/cloud-shell1.png)

    ![Azure portal with a cloud shell pane](./images/cl2.png)
   
2. In the **Getting Started** menu,choose **No storage account required (1)**,select your default **Subscription (2)** from the dropdown and click on **Apply (3)**

   ![Azure portal with a cloud shell pane](./images/cl3.png)

3. Note that Cloud Shell can be resized by dragging the separator bar at the top of the pane, or by using the—, **&#9723;**, and **X** icons at the top right of the pane to minimize, maximize, and close the pane. For more information about using the Azure Cloud Shell, see the [Azure Cloud Shell documentation](https://docs.microsoft.com/azure/cloud-shell/overview).

4. In the PowerShell pane, enter the following commands to clone this repository:

    ```powershell
    rm -r dp-203 -f
    git clone https://github.com/MicrosoftLearning/dp-203-azure-data-engineer dp-203
    ```

5. After the repository has been cloned, enter the following commands to change to the folder for this exercise, and run the **setup.ps1** script it contains:

    ```powershell
    cd dp-203/Allfiles/labs/10
    ./setup.ps1
    ```

6. If prompted, choose which subscription you want to use (this will only happen if you have access to multiple Azure subscriptions).
7. When prompted, enter a suitable password to be set for your Azure Synapse SQL pool.

    > **Note**: Be sure to remember this password!

8. Wait for the script to complete - this typically takes around 10 minutes, but in some cases may take longer. While you're waiting, review the [Data flows in Azure Synapse Analytics](https://learn.microsoft.com/azure/synapse-analytics/concepts-data-flow-overview) article in the Azure Synapse Analytics documentation.

## Task 2: View source and destination data stores

The source data for this exercise is a text file containing product data. The destination is a table in a dedicated SQL pool. Your goal is to create a pipeline that encapsulates a data flow in which the product data in the file is loaded into the table; inserting new products and updating existing ones.

In this task, you will be verifying the data stores by checking the files using Synapse Studio

1. After the script has completed, in the Azure portal, go to the **dp203-*xxxxxxx*** resource group that it created, and select your Synapse workspace.

2. In the **Overview** page for your Synapse Workspace, in the **Open Synapse Studio** card, select **Open** to open Synapse Studio in a new browser tab; signing in if prompted.

   ![](images/labimg2.png)

3. On the left side of Synapse Studio, use the ›› icon to expand the menu - this reveals the different pages within Synapse Studio that you’ll use to manage resources and perform data analytics tasks.

4. On the **Manage** page, on the **SQL pools** tab, select the row for the **sql*xxxxxxx*** dedicated SQL pool and use its **&#9655;** icon to start it; confirming that you want to resume it when prompted.

     Resuming the pool can take a few minutes. You can use the **&#8635; Refresh** button to check its status periodically. The status will show as **Online** when it's ready. While you're waiting, continue with the steps below to view the source data.

5. On the **Data** page, view the **Linked** tab and verify that your workspace includes a link to your Azure Data Lake Storage Gen2 storage account, which should have a name similar to **synapse*xxxxxxx* (Primary - datalake*xxxxxxx*)**.

   ![](images/labimg3.png)

6. Expand your storage account and verify that it contains a file system container named **files (primary)**.

7. Select the files container, and note that it contains a folder named **data**.

   ![](images/labimg15.png)

8. Open the **data** folder and observe the **Product.csv** file it contains.

9. Right-click **Product.csv** and select **Preview** to see the data it contains. Note that it contains a header row and some records of product data.

   ![](images/labimg16.png)

10. Return to the **Manage** page and ensure that your dedicated SQL pool is now online. If not, wait for it.

11. In the **Data** page, on the **Workspace** tab, expand **SQL database**, your **sql*xxxxxxx* (SQL)** database, and its **Tables**.

    ![](images/labimg17.png)

12. Select the **dbo.DimProduct** table. Then in its **...** menu, select **New SQL script** > **Select TOP 100 rows**; which will run a query that returns the product data from the table - there should be a single row.

    ![](images/labimg18.png)

## Task 3: Implement a pipeline

In this task, you will implement an Azure Synapse Analytics pipeline that contains a dataflow encapsulating the logic to ingest the data from the text file, lookup the surrogate **ProductKey** column for products that already exist in the database, and then insert or update rows in the table accordingly.

### Task 3.1: Create a pipeline with a data flow activity

1. In Synapse Studio, select the **Integrate** page. Then in the **+** menu select **Pipeline** to create a new pipeline.
2. In the **Properties** pane for your new pipeline, change its name from **Pipeline1** to **Load Product Data**. Then use the **Properties** button above the **Properties** pane to hide it.
3. In the **Activities** pane, expand **Move & transform**; and then drag a **Data flow** to the pipeline design surface as shown here:

    ![Screenshot of a pipeline with a data flow activity.](./images/dataflow(1).png)

4. Under the pipeline design surface, in the **General** tab(at the bottom of the page), set the **Name** property to **LoadProducts**.
5. On the **Settings** tab, at the bottom of the list of settings, expand **Staging** and set the following staging settings:
    - **Staging linked service**: Select the **synapse*xxxxxxx*-WorkspaceDefaultStorage** linked service.
    - **Staging storage folder**: Replace **container** to **files** and replace **Directory** to **stage_products**.

### Task 3.2: Configure the data flow

1. At the top of the **Settings** tab for the **LoadProducts** data flow, for the **Data flow** property, select **+ New**.
2. In the **Properties** pane for the new data flow design surface that opens, set the **Name** to **LoadProductsData** and then hide the **Properties** pane. The data flow designer should look like this:

    ![Screenshot of an empty data flow activity.](./images/empty-dataflow(1).png)

### Task 3.3: Add sources

1. In the data flow design surface, in the **Add Source** drop-down list, select **Add Source**. Then configure the source settings as follows:
    - **Output stream name**: ProductsText
    - **Description**: Products text data
    - **Source type**: Integration dataset
    - **Dataset**: Select **+ New** to add new dataset with the following properties:
        - **New integration dataset**: Select **Azure Data lake Storage Gen2** and click **Continue**.
        - **Format**: Delimited text and click **Continue**.
        - **Name**: Products_Csv
        - **Linked service**: synapse*xxxxxxx*-WorkspaceDefaultStorage
        - **File path**: files/data/Product.csv
        - **First row as header**: Selected
        - **Import schema**: From connection/store
        - Click on **OK**.
    - **Allow schema drift**: Selected
2. On the **Projection** tab for the new **ProductsText** source, set the following data types:
    - **ProductID**: string
    - **ProductName**: string
    - **Color**: string
    - **Size**: string
    - **ListPrice**: decimal
    - **Discontinued**: boolean
3. Add a second source with the following properties:
    - **Output stream name**: ProductTable
    - **Description**: Product table
    - **Source type**: Integration dataset
    - **Dataset**: Select **+ New** to add a **New** dataset with the following properties:
        - **New integration dataset**: Select **Azure Synapse Analytics** and click **Continue**.
        - **Name**: DimProduct
        - **New Linked service**: Select **+ New** from the dropdown to create a **New** linked service with the following properties:
            - **Name**: Data_Warehouse
            - **Description**: Dedicated SQL pool
            - **Connect via integration runtime**: AutoResolveIntegrationRuntime
            - **version**: Legacy
            - **Account selection method**: From Azure subscription
            - **Azure subscription**: Select your Azure subscription
            - **Server name**: synapse*xxxxxxx* (Synapse workspace)
            - **Database name**: sql*xxxxxxx*
            - **SQL pool**: sql*xxxxxxx*
            - **Authentication type**: System Assigned Managed Identity
            - Click on **Create**.
        - **Table name**: dbo.DimProduct
        - **Import schema**: None
        -  Click **OK**.
    - **Allow schema drift**: Selected

4. Select **Open** under **Dataset** to add the table and import the schema

    ![Screenshot of an empty data flow activity.](./images/lab1-new1.png)

5. On the **connection** page, select the **dbo.DimProduct(1)** table from the dropdown and click on **Schema(2)**

    ![Screenshot of an empty data flow activity.](./images/lab1-new2.png)

6. On the **Schema** page, click on **Import schema** and naviage back to **ProductTable** source

    ![Screenshot of an empty data flow activity.](./images/lab1-new3.png)

7. On the **Projection** tab for the new **ProductTable** source, verify that the following data types are set:
    - **ProductKey**: integer
    - **ProductAltKey**: string
    - **ProductName**: string
    - **Color**: string
    - **Size**: string
    - **ListPrice**: decimal
    - **Discontinued**: boolean

8. Verify that your data flow contains two sources, as shown here:

    ![Screenshot of a data flow with two sources.](./images/dataflow_sources(1).png)

### Task 3.4: Add a Lookup

1. Select the **+** icon at the bottom right of the **ProductsText** source and select **Lookup**.
2. Configure the Lookup settings as follows:
    - **Output stream name**: MatchedProducts
    - **Description**: Matched product data
    - **Primary stream**: ProductText
    - **Lookup stream**: ProductTable
    - **Match multiple rows**: <u>Un</u>selected
    - **Match on**: Last row
    - **Sort conditions**: ProductKey ascending
    - **Lookup conditions**: ProductID == ProductAltKey
3. Verify that your data flow looks like this:

    ![Screenshot of a data flow with two sources and a lookup.](./images/dataflow_lookup(1).png)

    >**Note**: The lookup returns a set of columns from *both* sources, essentially forming an outer join that matches the **ProductID** column in the text file to the **ProductAltKey** column in the data warehouse table. When a product with the alternate key already exists in the table, the dataset will include the values from both sources. When the product dos not already exist in the data warehouse, the dataset will contain NULL values for the table columns.

### Task 3.5: Add an Alter Row

1. Select the **+** icon at the bottom right of the **MatchedProducts** Lookup and select **Alter Row**.
2. Configure the alter row settings as follows:
    - **Output stream name**: SetLoadAction
    - **Description**: Insert new, upsert existing
    - **Incoming stream**: MatchedProducts
    - **Alter row conditions**: Edit the existing condition and use the **+** button to add a second condition as follows (note that the expressions are *case-sensitive*):
        - InsertIf: `isNull(ProductKey)`
        - UpsertIf: `not(isNull(ProductKey))`
3. Verify that the data flow looks like this:

    ![Screenshot of a data flow with two sources, a lookup, and an alter row.](./images/dataflow_alterrow(1).png)

    >**Note**: The alter row step configures the kind of load action to perform for each row. Where there's no existing row in the table (the **ProductKey** is NULL), the row from the text file will be inserted. Where there's already a row for the product, an *upsert* will be performed to update the existing row. This configuration essentially applies a *type 1 slowly changing dimension update*.

### Task 3.6: Add a sink

1. Select the **+** icon at the bottom right of the **SetLoadAction** alter row step and select **Sink**.
2. Configure the **Sink** properties as follows:
    - **Output stream name**: DimProductTable
    - **Description**: Load DimProduct table
    - **Incoming stream**: SetLoadAction
    - **Sink type**: Integration dataset
    - **Dataset**: Select DimProduct
    - **Allow schema drift**: Selected
3. On the **Settings** tab for the new **DimProductTable** sink, specify the following settings:
    - **Update method**: Select **Allow insert** and **Allow Upsert**.
    - **Key columns**: Select **List of columns**, and then select the **ProductAltKey** column from dropdown.
4. On the **Mappings** tab for the new **DimProductTable** sink, clear the **Auto mapping** checkbox and specify <u>only</u> the following column mappings:
    - ProductID: ProductAltKey
    - ProductsText@ProductName: ProductName
    - ProductsText@Color: Color
    - ProductsText@Size: Size
    - ProductsText@ListPrice: ListPrice
    - ProductsText@Discontinued: Discontinued
5. Verify that your data flow looks like this:

    ![Screenshot of a data flow with two sources, a lookup, an alter row, and a sink.](./images/dataflow-sink(1).png)

    >**Note**: In the output column section if there is any extra column apart from the above mentioned column, kindly delete it.

## Task 4: Debug the Data Flow

In this task, you will be debugging the dataflow without publishing it..

1. At the top of the data flow designer, enabled **Data flow debug**. Review the default configuration and select **OK**, then wait for the debug cluster to start (which may take a few minutes).

   ![](images/labimg21.png)

2. In the data flow designer, select the **DimProductTable** sink and view its **Data preview** tab.

   >**Note**: kindly collapse **Integrate** pane to view **Data preview** tab.
   
3. Use the **&#8635; Refresh** button to refresh the preview, which has the effect of running data through the data flow to debug it.

   ![](images/labimg20.png)

4. Review the preview data, noting that it indicates one upserted row (for the existing *AR5381* product), indicated by a **<sub>*</sub><sup>+</sup>** icon; and ten inserted rows, indicated by a **+** icon.

   ![](images/labimg19.png)

## Task 5: Publish and run the pipeline

In this task, you will be publishing the pipeline as you have reviewed it and run it.

1. Use the **Publish all** button to publish the pipeline (and any other unsaved assets) and on **Publish all** pane click on **Publish**.

   ![](images/labimg22.png)

2. When publishing is complete, close the **LoadProductsData** data flow pane and return to the **Load Product Data** pipeline pane.

3. At the top of the pipeline designer pane, select **Add trigger** menu, click **Trigger now**. Then select **OK** to confirm you want to run the pipeline.

    >**Note**: You can also create a trigger to run the pipeline at a scheduled time or in response to a specific event.

4. When the pipeline has started running, on the **Monitor** page, view the **Pipeline runs** tab and review the status of the **Load Product Data** pipeline.

   ![](images/labimg23.png)

    >**Note**: The pipeline may take five minutes or longer to complete. You can use the **&#8635; Refresh** button on the toolbar to check its status.

5. When the pipeline run has succeeded, on the **Data** page, use the **...** menu for the **dbo.DimProduct** table in your SQL database to run a query that selects the top 100 rows. The table should contain the data loaded by the pipeline.

    ![Screenshot of an empty data flow activity.](./images/lab1-new4.png)


> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
  - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
  - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
  - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.

<validation step="85f12513-502b-47c1-9ac9-d820218a0f93" />

## Summary

In this lab, you have accomplished the following: you have viewed both the source and destination data stores to understand the data flow. You have implemented a pipeline to facilitate data movement and transformations. You have debugged the Data Flow to ensure everything functioned as expected. Finally, you have published and run the pipeline, completing the data integration process.

## You have successfully completed the lab.
