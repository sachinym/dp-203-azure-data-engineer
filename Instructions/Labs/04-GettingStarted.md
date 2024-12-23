# Getting started with Azure Databricks Analytics & Engineering Scenarios

### Overall Estimated Duration: 60 Minutes

## Overview

SQL is an industry-standard language for querying and manipulating data. Many data analysts perform data analytics by using SQL to query tables in a relational database. Azure Databricks includes SQL functionality that builds on Spark and Delta Lake technologies to provide a relational database layer over files in a data lake.

In this lab, you'll learn about Azure Databricks that provides SQL Warehouses that enable data analysts to work with data using familiar relational SQL queries.

## Objective

Learn how to provision an Azure Databricks workspace, start and configure SQL Warehouses, create custom database schemas, upload and manage data in tables, write SQL queries to analyze data, and build dashboards to visualize the results, providing a hands-on experience with SQL-based data analytics on Azure Databricks.

- **Provision an Azure Databricks workspace**: Set up an Azure Databricks workspace using a script to manage clusters, data, and resources in Azure. 

- **View and start a SQL Warehouse:** Access the Azure Databricks workspace, view the SQL Warehouse, and start it to allow for SQL-based data queries and management.

- **Create a database schema:** Create a custom schema in Databricks to organize and store tables and data efficiently, enabling better data management for analytics.

- **Create a table:** Upload a CSV file into Databricks and create a table in the newly created schema to structure the data for querying.

- **Create a query:** Write and execute a SQL query to retrieve data from the "products" table in the "adventureworks" schema to analyze product-related information.

- **Create a dashboard:** Build a dashboard in Databricks by visualizing the results from the query on the "products" table, using a bar chart to display product counts by category.

## Prerequisites

Participants should have:

- **Azure Databricks and SQL Warehouses:** Familiarity with the Azure Databricks platform, including creating and managing SQL Warehouses for data querying and analysis.

- **Understanding of Cloud Shell:** Experience with using Azure Cloud Shell for running scripts and managing Azure resources.

- **SQL and Data Modeling:** Knowledge of SQL syntax for creating databases, tables, and queries, as well as basic data modeling concepts for organizing data efficiently.

- **Dashboard Creation and Data Visualization:** Familiarity with creating and customizing dashboards to visualize and interpret data effectively.

## Architecture

The architecture involves using **Azure Databricks** platform as the central platform for managing and analyzing data. Azure Databricks integrates with cloud storage, such as **Azure Data Lake**, to store large datasets. It leverages **SQL Warehouses**, which act as compute clusters optimized for running SQL queries on data stored in Delta Lake. Data is organized into custom database schemas, and tables are created from uploaded data files, such as CSVs. SQL queries are executed to retrieve and analyze data, and visualizations are created to present the results. Finally, dashboards are built to display these visualizations and share insights with stakeholders, providing an end-to-end solution for data analytics and visualization in the cloud.

## Architecture Diagram

   ![Azure portal with a cloud shell pane](./Lab-Scenario-Preview/media/lab04-databricks.png)

## Explanation of Components

The architecture for this lab involves the following key components:

- **SQL Warehouses:** SQL Warehouses in Azure Databricks provide a scalable compute engine for running SQL queries on large datasets. They offer optimized performance for querying data stored in Delta Lake and are essential for efficient data processing and analytics in the lab.

- **Delta Lake:** Delta Lake is a storage layer that ensures ACID compliance for data stored in Azure Databricks. It allows for efficient data querying, incremental data updates, and versioning, making it crucial for managing and processing large volumes of data during analysis.

- **Databricks Notebooks and Dashboards:** Databricks notebooks are interactive environments where users can write and execute SQL queries, visualize results, and collaborate on data analysis. Dashboards provide a way to visualize query results in an easily accessible format, helping share insights across teams.

## Getting Started with Lab
 
Once you're ready to dive in, your virtual machine and lab guide will be right at your fingertips within your web browser.
 
![Access Your VM and Lab Guide](../Labs/images/labguide-1.png)

### Virtual Machine & Lab Guide
 
Your virtual machine is your workhorse throughout the workshop. The lab guide is your roadmap to success.
 
## Exploring Your Lab Resources
 
To get a better understanding of your lab resources and credentials, navigate to the **Environment** tab.
 
![Explore Lab Resources](../Labs/images/env-1.png)
 
## Utilizing the Split Window Feature
 
For convenience, you can open the lab guide in a separate window by selecting the **Split Window** button from the Top right corner.
 
![Use the Split Window Feature](../Labs/images/spl.png)
 
## Managing Your Virtual Machine
 
Feel free to start, stop, or restart your virtual machine as needed from the **Resources** tab. Your experience is in your hands!
 
![Manage Your Virtual Machine](../Labs/images/res.png)

## **Lab Duration Extension**

1. To extend the duration of the lab, kindly click the **Hourglass** icon in the top right corner of the lab environment. 

    ![Manage Your Virtual Machine](../Labs/images/gext.png)

    >**Note:** You will get the **Hourglass** icon when 10 minutes are remaining in the lab.

2. Click **OK** to extend your lab duration.
 
   ![Manage Your Virtual Machine](../Labs/images/gext2.png)

3. If you have not extended the duration prior to when the lab is about to end, a pop-up will appear, giving you the option to extend. Click **OK** to proceed.
 
## Let's Get Started with Azure Portal
 
1. On your virtual machine, click on the Azure Portal icon as shown below:
 
   ![Launch Azure Portal](../Labs/images/sc900-image(1).png)

 
2. You'll see the **Sign into Microsoft Azure** tab. Here, enter your credentials:
 
   - **Email/Username:** <inject key="AzureAdUserEmail"></inject>
 
       ![Enter Your Username](../Labs/images/sc900-image-1.png)
 
3. Next, provide your password:
 
   - **Password:** <inject key="AzureAdUserPassword"></inject>
 
      ![Enter Your Password](../Labs/images/sc900-image-2.png)
 
4. If prompted to stay signed in, you can click "No."

   ![](../Labs/images/Sign-in-no.png)

6. If **Action Required** pop-up window appears, click on **Ask later**.

     ![](../Labs/images/ActionRequired.png)
 
7. If a **Welcome to Microsoft Azure** pop-up window appears, simply click "Cancel" to skip the tour.

    ![](../Labs/images/Azure-cancel-tour.png)

## Support Contact
 
The CloudLabs support team is available 24/7, 365 days a year, via email and live chat to ensure seamless assistance at any time. We offer dedicated support channels tailored specifically for both learners and instructors, ensuring that all your needs are promptly and efficiently addressed.

Learner Support Contacts:
- Email Support: cloudlabs-support@spektrasystems.com
- Live Chat Support: https://cloudlabs.ai/labs-support

Click "Next" from the bottom right corner to embark on your Lab journey!
 
   ![Start Your Azure Journey](../Labs/images/sc900-image(3).png)
 
### Happy Learning!!