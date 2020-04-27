## Setting-up AWS RDS Postgres, Deploy a database and build a power Bi dashboard 

**Project description:** This is a guided project in which we describe concisely how to set-up an aws rds 
postgres database instance using terraform, deploy a databse and connect to it to build a simple Power BI dashboard.

The steps we will follow are:
  - [ ] Deploy an AWS RDS POSTGRES INSTANCE using TERRAFORM (Free Tier)
  - [ ] Check connection to the POSTGRES INSTANCE using PGADMIN 
  - [ ] Load a relational database (NorthWind) to the RDS POSTGRES instance using PSQL
  - [ ] Connect Power BI Desktop to the RDS POSTGRES NorthWind database
  - [ ] Build a Simple Power BI Dashboard using the NorthWind dabase
  - [ ] Deploy the Power BI dashboard

---

### 1. Deploy an AWS RDS POSTGRES INSTANCE using TERRAFORM (Free Tier)

  #### Pre-requisite
  * An AWS Account set-up (including IAM credentials)
  * Be familiar with AWS RDS and VPC settings
  * Latest version of TERRAFORM CLI installed and set-up on your local machine (https://www.terraform.io/downloads.html)

  #### AWS RDS in a nutshell
  AWS provides a relational database cloud service called RDS (Relational Database Service). 
  This means that database instances can be deployed and managed in the cloud by AWS (https://aws.amazon.com/rds/)

  Currently, AWS supports multiple flavors of databases, namely:
  * Aurora
  * Postgres SQL
  * MySQL
  * MariaDB
  * Oracle
  * Microsodft SQl server

  <img src="images/RDS supported databases.PNG?raw=true"/>

  #### AWS RDS free tier
  AWS provides an RDS free tier service for small cloud instances and open source databases such as MySQL, 
  Postgres (https://aws.amazon.com/rds/free/)
  > *750 hours of Amazon RDS Single-AZ db.t2.micro Instance usage running MySQL, MariaDB, PostgreSQL, Oracle BYOL 
  > or SQL Server  (running SQL Server Express Edition) – enough hours to run a DB Instance continuously each month*

  We will be using a free tier RDS database instance.

  #### POSTGRES database in a nutshell
  PostgreSQL is a general purpose and object-relational database management system. It is open-source, 
  free and used by many well know companies including uber, netflix, and spotify
  (https://www.postgresql.org/about/press/faq/)

  We will be deploying a Postgres database instance.

  #### TERRAFORM in a nutshell
  Terraform is a tool for building, changing, and versioning cloud infrastructure services efficiently. 
  It is cloud vendor agnostic (i.e. supports Azure, AWS, GCP etc.) and uses infrastrcuture as Code (IAC) 
  to deploy and manage  most cloud services (https://www.terraform.io/intro/index.html). 
  
  Essentially, terraform lets users set-up cloud services using configuration files which are used to deploy, 
  track, update or shutdown specified cloud services.

  We will be using terraform to deploy our RDS database instance in AWS.

### Deploy an AWS RDS instance using TERRFORM
  1. Create the TERRAFORM configuration file to deploy the AWS RDS POSTGRES instance (file should have a .tf extension) 

  The following provides the minimum configuration settings to dploy an AWS RDS POSTGRES free tier instance: 
  ```
    provider "aws" {
      region = "ap-southeast-2"
    }
    resource "aws_db_instance" "dsdj-postgres-db-instance" {
      allocated_storage    = 20
      #db_subnet_group_name = "db-subnetgrp"
      engine               = "postgres"
      engine_version       = "11.5"
      identifier           = "dsdj-postgres-db"
      instance_class       = "db.t2.micro"
      password             = "mypostgrespassword"
      skip_final_snapshot  = true
      storage_encrypted    = false
      publicly_accessible    = true
      username             = "postgres"
      apply_immediately = true
    }
  ```

  A few key RDS settings to understand:
  * Replace *mypostgrespassword* with a strong password to access the PostGres databse
    We use aws AWS' db.t2.micro instance so for the free tier service. Anything else, will trigger AWS costs.
  * Since I am based in New Zealand, I have am setting up the instance in the ap-southeast-2 region.
  * I am making the database and the data accessible from any IP by setting the publicly_accessible setting to true. 
    **Beware ! This means that the instance is visible and can be reached anywhere in the word (ie. without having to hop 
    through a proxy AWS EC2 instance for security).**
  * The postgres database user login and associated passwords are set in the 'username' and 'password' s
    ettings in the terraform files.
  * The postgres database verison is set in the engine_version (11.5 in this case).

  2. Deploy the AWS RDS POSTGRES instance using terraform from the CLI 

      - Initialize TERRAFORM using the following command: ``` terraform init ```

      - Check what TERRAFORM is planning to deploy in AWS using the following command: ``` terraform plan ```

        <kbd> <img src="images/terraform-plan.PNG?raw=true"/> </kbd>

      - Deploy our RDS POSTGRES instance in AWS using: ``` terraform deploy ```

        <kbd> <img src="images/terraform-apply.PNG?raw=true"/> </kbd>

      - It looks good, an instance seems to have been created without any error !

        <kbd> <img src="images/terraform-apply2.PNG?raw=true"/> </kbd>

      - Validate manually that TERRAFORM has properly deployed the RDS POSTGRES instance in AWS by 
      checking into the AWS RDS online web console:

        <kbd> <img src="images/aws-rds-instance check.png?raw=true"/> </kbd>



  **Remember to release the RDS instance once you are finished to avoid running out of AWS free tier resource !**
    Use the following command to shut down the database instance : ``` terraform destroy ```


#### Tracking our progress
  - [X] Deploy an AWS RDS POSTGRES INSTANCE using TERRAFORM (Free Tier)
  - [ ] Check connection to the POSTGRES INSTANCE using PGADMIN 
  - [ ] Load a relational database (NorthWind) to the RDS POSTGRES instance using PSQL
  - [ ] Connect Power BI Desktop to the RDS POSTGRES NorthWind database
  - [ ] Build a Simple Power BI Dashboard using the NorthWind dabase
  - [ ] Deploy the Power BI dashboard

reference: https://tech.instacart.com/terraforming-rds-part-1-7cc78f92b24d?gi=bbb72a10f9d1

---

### 2. Check the connection to the POSTGRES INSTANCE using PGADMIN 

  Now that we have a POSTGRES database instance running in AWS, we will be checking we have proper access 
  to the database from our local environment.

  #### Pre-requisite
  * pgAdmin installed (https://www.pgadmin.org/)

  #### PGADMIN database in a nutshell
  pgAdmin is POSTGRES SQL administration tool. It is a web based gaphical user interface to connect 
  to POSTGRES databases and facilitates the creation, maintenance, querying and use of 
  database objects and tables. 

  #### Identify the AWS RDS POSTGRES database host information
  You can use AWS RDS console to check for the host connection information

  <kbd> <img src="images/AWS RDS Console.PNG"> </kbd>

  #### Connect to the newly created AWS RDS POSTGRES instance 
  1. Use pgAdmin to connect to our database instance - create a server

  <kbd> <img src="images/pgAdmin 1.PNG?raw=true"/> </kbd>

  2. Use pgAdmin to connect to our database instance - provide host, port, user login and 
  password connection details

  <kbd> <img src="images/pgAdmin 2.PNG?raw=true"/> </kbd>

  3. Check that you can see the live connection in the pgAdmin UI

  <kbd> <img src="images/pgAdmin 3.PNG?raw=true"/> </kbd>

#### Tracking our progress
  - [X] Deploy an AWS RDS POSTGRES INSTANCE using TERRAFORM (Free Tier)
  - [X] Check connection to the POSTGRES INSTANCE using PGADMIN 
  - [ ] Load a relational database (NorthWind) to the RDS POSTGRES instance using PSQL
  - [ ] Connect Power BI Desktop to the RDS POSTGRES NorthWind database
  - [ ] Build a Simple Power BI Dashboard using the NorthWind dabase
  - [ ] Deploy the Power BI dashboard

---
### 3. Load a relational database (NorthWind) to the RDS POSTGRES instance using PSQL
  We now have a POSTGRES SQL database instance running in AWS, let's deploy the NorthWind 
  relational tables and content to the database.

  > Northwind Traders Access database is a sample database.
  > The Northwind database contains sales data for a fictitious company called Northwind > Traders, 
  > which imports and exports specialty foods from around the world.

#### Pre-requisite
  * PSQL CLI installed (http://postgresguide.com/utilities/psql.html)
  * NorthWind database SQL script downloaded to your local environment
  (https://github.com/pthom/northwind_psql/blob/master/northwind.sql)

#### PSQL database in a nutshell
  PSQL is the interactive CLI to interact with Postgres databases. 
  PSQL lets user create, query, update and delete POSTGRES tables and databases through the command line. 

#### Create the NorthWind database using PSQL

1. Deploy the NorthWind database sql script

   - Create the NorthWind database
   ```
   psql -h dsdj-postgres-db.clpvihbunw2c.ap-southeast-2.rds.amazonaws.com -U postgres -p 5432 -c "CREATE DATABASE \"NorthWind\";"
   ```

      <kbd> <img src="images/PSQL 1.PNG?raw=true"/> </kbd>

   - Create the NorthWind tables
   ```
   psql -h dsdj-postgres-db.clpvihbunw2c.ap-southeast-2.rds.amazonaws.com -U postgres -p 5432 -d NorthWind < northwind.sql
   ```

      <kbd> <img src="images/PSQL 2.PNG?raw=true"/> </kbd>

2. Check that the NorthWind database was properly created with PGADMIN or PSQL

   - PGADMIN 

      <kbd> <img src="images/pgAdmin 4.PNG?raw=true"/> </kbd>

   - PSQL, Once connected to the Northwind Database use: ``` \d ```

      <kbd> <img src="images/PSQL 3.PNG?raw=true"/> </kbd>


#### Tracking our progress
  - [X] Deploy an AWS RDS POSTGRES INSTANCE using TERRAFORM (Free Tier)
  - [X] Check connection to the POSTGRES INSTANCE using PGADMIN 
  - [X] Load a relational database (NorthWind) to the RDS POSTGRES instance using PSQL
  - [ ] Connect Power BI Desktop to the RDS POSTGRES NorthWind database
  - [ ] Build a Simple Power BI Dashboard using the NorthWind dabase
  - [ ] Deploy the Power BI dashboard

---

### 4. Connect Power BI Desktop to the RDS POSTGRES NorthWind database
  Now that we have database instance running withthe NorthWind tables loaded, we turn our attention 
  to connecting to the tables with Power BI

#### Pre-requisite
  * Power BI desktop installed (https://powerbi.microsoft.com/en-us/downloads/)

#### Power BI in a nutshell
  Power BI is an ETL and visualization Microsoft tool for business analytics and reporting. 
  It provides interactive visualizations and business intelligence capabilitieto create reports 
  and dashboards which can be deployed in Microsoft and Azure's environment. 

  1. Launch Power BI Desktop
  
  <kbd> <img src="images/power bi 1.PNG?raw=true"/> </kbd>

  2. Connect to the POSTGRES NorthWind database using AWS host connection information

   - Use get data and find the Postgres data source in the list 
    
      <kbd> <img src="images/power bi 2.PNG?raw=true"/> </kbd>
    
   - Enter the Postgres RDS host information and database name
    
      <kbd> <img src="images/power bi 3.PNG?raw=true"/> </kbd>
    
   - Enter the Postgres RDS host information and database name
    
      <kbd> <img src="images/power bi 4.PNG?raw=true"/> </kbd>

  3. Load to the POSTGRES NorthWind tables 

   - Power BI will show the available NorthWind database tables available
      
      <kbd> <img src="images/power bi 5.PNG?raw=true"/> </kbd>

   - Select and load the NorthWind database tables 
      
      <kbd> <img src="images/power bi 6.PNG?raw=true"/> </kbd>

#### Tracking our progress
 - [X] Deploy an AWS RDS POSTGRES INSTANCE using TERRAFORM (Free Tier)
 - [X] Check connection to the POSTGRES INSTANCE using PGADMIN 
 - [X] Load a relational database (NorthWind) to the RDS POSTGRES instance using PSQL
 - [X] Connect Power BI Desktop to the RDS POSTGRES NorthWind database
 - [ ] Build a Simple Power BI Dashboard using the NorthWind dabase
 - [ ] Deploy the Power BI dashboard


For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).
