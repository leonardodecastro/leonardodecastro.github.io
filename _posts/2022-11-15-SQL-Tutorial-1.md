# SQL with Google Colab Tutorial 1

> PostgreSQL with Google Colab Tutorial 1

- toc: true 
- badges: true
- comments: true
- categories: [PostgreSQL wit Google Colab, SQL]
- image: images/sql_1.png

## 1) Aknowledge the sources used for this tutorial

This post was created using information from 2 sources. The first post explains how to use PostgreSQL with Google Colab. The second source is a tutorial by at Data.World. The links to these sources are found below:
- https://thivyapriyaa.medium.com/setting-up-postgresql-on-google-colab-4d02166939fc
- https://data.world/classrooms/guide-to-data-analysis-with-sql-and-datadotworld/workspace/file?filename=01_select_data.md

## 2) Import libraries


```python
import psycopg2
import pandas as pd
pd.options.mode.chained_assignment = None  # default='warn'
```

## 3) Install PostgreSQL

### Colab is a linux environment. Thus, we can use linux commands to install  PostgreSQL on it

You might need to run the following cell twice if the database is not created when you run it just once.


```python
# Install postgresql server
!sudo apt-get -y -qq update   # Update the PostgreSQL in case they are not 
!sudo apt-get -y -qq install postgresql # Install PostgreSQL 
!sudo service postgresql start # Start the PostgreSQL service

# Setup a password `postgres` for username `postgres`
!sudo -u postgres psql -U postgres -c "ALTER USER postgres PASSWORD 'postgres';"

# Setup a database with name `tfio_demo` to be used
!sudo -u postgres psql -U postgres -c 'DROP DATABASE IF EXISTS tfio_demo;'  # Drop databases if they exist
!sudo -u postgres psql -U postgres -c 'CREATE DATABASE tfio_demo;' # Drop databases if they exist
```

     * Starting PostgreSQL 10 database server
       ...done.
    ALTER ROLE
    DROP DATABASE
    CREATE DATABASE
    

### Setup necessary environmental variables


```python
%env DB_NAME=tfio_demo
%env DB_HOST=localhost
%env DB_PORT=5432
%env DB_USER=postgres
%env DB_PASS= postgres
```

    env: DB_NAME=tfio_demo
    env: DB_HOST=localhost
    env: DB_PORT=5432
    env: DB_USER=postgres
    env: DB_PASS=postgres
    

## 4) Create tables within the database

### 4.1) Drop tables if they already exist


```python
#### Table 1: Annual visitation of the Big Bend park between 1904 and 2015
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER-d $DB_NAME -c "DROP TABLE IF EXISTS visitation;"

#### Table 2: Cats VS Dogs
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER-d $DB_NAME -c "DROP TABLE IF EXISTS animals;"

#### Table 3: Counties
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER-d $DB_NAME -c "DROP TABLE IF EXISTS counties;"

#### Table 4: Products
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER-d $DB_NAME -c "DROP TABLE IF EXISTS products;"

#### Table 5: Sales
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER-d $DB_NAME -c "DROP TABLE IF EXISTS sales;"

#### Table 6: Sales 2016
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER-d $DB_NAME -c "DROP TABLE IF EXISTS sales2016;"

#### Table 7: Stores
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER-d $DB_NAME -c "DROP TABLE IF EXISTS stores;"

#### Table 8: Convenience Stores
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER-d $DB_NAME -c "DROP TABLE IF EXISTS convenience;"
```

### 4.2) Create tables


```python
#### Table 1: Annual visitation of the Big Bend park between 1904 and 2015
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME \
-c "CREATE TABLE visitation(visitation_index SERIAL PRIMARY KEY,year INT,visitor_count INT,total_visitors INT);"

#### Table 2: Cats VS Dogs
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME \
-c "CREATE TABLE animals(animal_index SERIAL PRIMARY KEY, location TEXT, region TEXT, number_of_households_in_1000 INT, \
                         percentage_of_households_with_pets DECIMAL(5,3), number_of_pet_households_in_1000 INT, percentage_of_dog_owners DECIMAL(5,3), \
                         dog_owning_households_1000s INT, mean_number_of_dogs_per_household DECIMAL(5,3), dog_population_in_1000 INT, \
                         percentage_of_cat_owners DECIMAL(5,3), cat_owning_households INT, mean_number_of_cats DECIMAL(5,3), cat_population INT);"

#### Table 3: Counties
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME \
-c "CREATE TABLE counties(counties_index SERIAL PRIMARY KEY, county TEXT, county_number INT, population INT);"

#### Table 4: Products
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME \
-c "CREATE TABLE products(products_index SERIAL PRIMARY KEY, item_no INT, category_name TEXT, item_description TEXT, \
                          vendor INT, vendor_name	TEXT, bottle_size INT, pack INT, inner_pack INT, age INT, proof INT, \
                           list_date DATE, upc BIGINT, scc BIGINT, bottle_price MONEY, shelf_price DECIMAL, case_cost DECIMAL);"
#### Table 5: Sales
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME \
-c "CREATE TABLE sales(sales_index SERIAL PRIMARY KEY, date DATE,	convenience_store TEXT, \
                       store INT, county_number INT,	county TEXT,	category TEXT,	category_name TEXT,	vendor_no INT,	\
                       vendor TEXT,	item INT,	description TEXT,	pack INT,	liter_size INT,	state_btl_cost MONEY,	 \
                       btl_price MONEY,	bottle_qty INT,	total DECIMAL);"

#### Table 6: Sales 2016
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME \
-c "CREATE TABLE sales_2016(sales2016_index SERIAL PRIMARY KEY, invoice_item_number TEXT, date DATE, store_number INT, store_name TEXT, address TEXT, \
       city TEXT, zip_Code TEXT, store_location TEXT, county_number TEXT, county TEXT, category TEXT, category_name TEXT, vendor_number TEXT, \
       vendor_name TEXT, item_number INT, item_description TEXT, pack INT, bottle_volume_ml INT, state_bottle_cost DECIMAL, state_bottle_retail DECIMAL, \
       bottles_sold INT, sale_dollars DECIMAL, volume_sold_liters DECIMAL, volume_sold_gallons DECIMAL);"

#### Table 7: Stores
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME \
-c "CREATE TABLE stores(stores_index SERIAL PRIMARY KEY, store INT, name TEXT, store_status TEXT, store_address TEXT, address_info TEXT);"

#### Table 8: Convenience Stores
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME \
-c "CREATE TABLE convenience(convenience_index SERIAL PRIMARY KEY, store INT,county TEXT);"
```

## 5) Populate the tables

### 5.1) Import CSV files


```python
# This line will disappear in the portfolio page
#### 5.1) Import the csv for the "visitation" table
!wget https://raw.githubusercontent.com/leonardodecastro/data/main/visitation.csv -nv

#### 5.2) Import the csv for the "animals" table
!wget https://raw.githubusercontent.com/leonardodecastro/data/main/animals.csv -nv

#### 5.3) Import the csv for the "counties" table
!wget https://raw.githubusercontent.com/leonardodecastro/data/main/counties.csv -nv

#### 5.4) Import the csv for the "products" table
!wget https://raw.githubusercontent.com/leonardodecastro/data/main/products.csv -nv

#### 5.5) Import the csv for the "sales" table
!wget https://raw.githubusercontent.com/leonardodecastro/data/main/sales.zip -nv
!unzip '/content/sales.zip'

#### 5.6) Import the csv for the "sales2016" table
!wget https://raw.githubusercontent.com/leonardodecastro/data/main/sales_2016.zip -nv
!unzip '/content/sales_2016.zip'

#### 5.7) Import the csv for the "stores" table
!wget https://raw.githubusercontent.com/leonardodecastro/data/main/stores.csv -nv

#### 5.8) Import the csv for the "convenience" table
!wget https://raw.githubusercontent.com/leonardodecastro/data/main/convenience.csv -nv
```

### 5.2) Populate the tables


```python
# This line will disappear in the portfolio page
#### 5.1) Populate the "visitation" table
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME \
-c "\COPY visitation(year, visitor_count, total_visitors) FROM \
'visitation.csv' ( format csv, header, delimiter ',', encoding 'win1252', null ' ' );"

#### 5.2) Populate the "animals" table
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME \
-c "\COPY animals(location, region, number_of_households_in_1000, percentage_of_households_with_pets, number_of_pet_households_in_1000,  \
                     percentage_of_dog_owners, dog_owning_households_1000s, mean_number_of_dogs_per_household , dog_population_in_1000, \
                     percentage_of_cat_owners, cat_owning_households, mean_number_of_cats , cat_population) FROM \
                     'animals.csv' ( format csv, header, delimiter ',', encoding 'win1252', null ' ' );"

#### 5.3) Populate the "counties" table
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME \
-c "\COPY counties(county, county_number, population) FROM \
'counties.csv' ( format csv, header, delimiter ',', encoding 'win1252', null ' ' );"

#### 5.4) Populate the "products" table
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME \
-c "\COPY products(item_no, category_name, item_description, vendor, vendor_name, bottle_size, pack, inner_pack, age, proof, \
                           list_date, upc, scc, bottle_price, shelf_price, case_cost) FROM \
'products.csv' ( format csv, header, delimiter ';', encoding 'win1252', null '');"

#### 5.5) Populate the "sales" table
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME \
-c "\COPY sales(date,	convenience_store, store, county_number,	county,	category,	category_name,	vendor_no,	\
                       vendor,	item,	description,	pack,	liter_size,	state_btl_cost,	btl_price,	bottle_qty,	total) \
                       FROM 'sales.csv' ( format csv, header, delimiter ',', encoding 'win1252', null ' ' );"

#### 5.6) Populate the "sales2016" table
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME \
-c "\COPY sales_2016(invoice_item_number, date, store_number, store_Name, address, \
       city, zip_code, store_location, county_number, county, category, category_name, vendor_number, \
       vendor_name, item_number, item_description, pack, bottle_volume_ml, state_bottle_cost, state_bottle_retail, \
       bottles_sold, sale_dollars, volume_sold_liters, volume_sold_gallons) FROM \
'sales_2016.csv' ( format csv, header, delimiter ',', encoding 'win1252', null ' ' );"

#### 5.7) Populate the "stores" table
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME \
-c "\COPY stores(store, name, store_status, store_address, address_info) FROM \
'stores.csv' ( format csv, header, delimiter ',', encoding 'win1252', null ' ' );"

#### 5.8) Populate the "convenience" table
!PGPASSWORD=$DB_PASS psql -q -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME \
-c "\COPY convenience(store, county) FROM \
'convenience.csv' ( format csv, header, delimiter ',', encoding 'win1252', null ' ' );"
```

## 6) Practice SQL

The introduction to the sub-sections of part 6 were extracted from the second source referenced in the first part of this script. 

### 6.1) SELECT, WHERE & ORDER BY commands

When accessing data using SQL, **Structured Query Language**, the two foundational parts of the command sequence are `SELECT` and `FROM`. Using `SELECT`, you choose the information you want to include in your report. `FROM` identifies the source table or file name from which to retrieve or calculate that information. This structure will look like:

 > **SELECT** *(desired data here)* <br>
 > **FROM** *(table name here)*

#### 6.1.1) Select all columns using asterisk (*).

Here we select all columns from the `visitation` table. 


```python
# Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute("SELECT * \
                FROM visitation")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]]).head()
```





  <div id="df-88a36d3d-b866-40b2-b308-f6d935af6acd">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>visitation_index</th>
      <th>year</th>
      <th>visitor_count</th>
      <th>total_visitors</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>1944</td>
      <td>1409</td>
      <td>15431947</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1945</td>
      <td>3205</td>
      <td>15431947</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>1946</td>
      <td>10037</td>
      <td>15431947</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>1947</td>
      <td>28652</td>
      <td>15431947</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>1948</td>
      <td>45670</td>
      <td>15431947</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-88a36d3d-b866-40b2-b308-f6d935af6acd')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-88a36d3d-b866-40b2-b308-f6d935af6acd button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-88a36d3d-b866-40b2-b308-f6d935af6acd');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




#### 6.1.2) Select certain columns of a given table.

Here we select the columns `year` and `vistor_count` from the `visitation` table. 


```python
# Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute(" SELECT visitation.year, visitation.visitor_count \
                 FROM visitation; ")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]]).head()
```





  <div id="df-5cfbea6d-3ee1-49aa-aee3-0498edd976ca">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>year</th>
      <th>visitor_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1944</td>
      <td>1409</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1945</td>
      <td>3205</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1946</td>
      <td>10037</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1947</td>
      <td>28652</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1948</td>
      <td>45670</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-5cfbea6d-3ee1-49aa-aee3-0498edd976ca')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-5cfbea6d-3ee1-49aa-aee3-0498edd976ca button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-5cfbea6d-3ee1-49aa-aee3-0498edd976ca');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




#### 6.1.3) Create aggregate measures (SUM).

Here we calculate the sum of all values for the column `vistor_count` from the `visitation` table. 


```python
# Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute(" SELECT SUM(visitation.visitor_count) \
                 FROM visitation; ")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]]).head(2)
```





  <div id="df-ec8b5c9c-2423-42e7-b9c3-c3fc437bdddc">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>sum</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15431947</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-ec8b5c9c-2423-42e7-b9c3-c3fc437bdddc')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-ec8b5c9c-2423-42e7-b9c3-c3fc437bdddc button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-ec8b5c9c-2423-42e7-b9c3-c3fc437bdddc');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




#### 6.1.4) Minimum, Maximum and Average Metrics.

Here we calculate the minimum, average and maximum values for the column `vistor_count` from the `visitation` table. 


```python
# Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute("SELECT MIN(visitation.visitor_count), AVG(visitation.visitor_count), MAX(visitation.visitor_count) \
                FROM visitation;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]]).head(2)
```





  <div id="df-65c9d6fa-befa-42e0-8f0a-0af6aabd687d">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>min</th>
      <th>avg</th>
      <th>max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1409</td>
      <td>214332.597222222222</td>
      <td>398583</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-65c9d6fa-befa-42e0-8f0a-0af6aabd687d')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-65c9d6fa-befa-42e0-8f0a-0af6aabd687d button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-65c9d6fa-befa-42e0-8f0a-0af6aabd687d');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




#### 6.1.5) WHERE and ORDER BY command

Here we select the columns `year` and `visitor_count` from the `visitation` table. The selection is limited to `visitor_count` values equal or greater than 300000. The results are organized by `year` in a descending order. 


```python
# Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute("SELECT visitation.year, visitation.visitor_count \
                FROM visitation WHERE visitation.visitor_count >= 300000 \
                ORDER BY visitation.year \
                DESC;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]]).head()
```





  <div id="df-b97a27d3-37c2-4ce9-9f67-77b8ac2974b2">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>year</th>
      <th>visitor_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2015</td>
      <td>381747</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2014</td>
      <td>314102</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2013</td>
      <td>316953</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2011</td>
      <td>361862</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2010</td>
      <td>372330</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-b97a27d3-37c2-4ce9-9f67-77b8ac2974b2')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-b97a27d3-37c2-4ce9-9f67-77b8ac2974b2 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-b97a27d3-37c2-4ce9-9f67-77b8ac2974b2');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




#### 6.1.6) Summary

The following summary is provided in the second article that was referenced in part 1:
- `SELECT` and `FROM` are the most essential parts of every query.
- Selected data may request all `(*)`, specific columns or calculated values.
- Formal referencing of data includes the sourcing table (table.data).
- `WHERE` is a valuable tool for filtering data for the desired output.
- `ORDER BY` sorts the query results, ascending `(ASC)` is the default.

### 6.2) FILTER & GROUP commands

Building on the basics of selecting desired data, add criteria to further refine the information retrieved for your report using conditional operators. These include command words such as `AND`, `OR`, `NOT`, `IN` and `BETWEEN`. They are added to the `SQL` query within the components of a `WHERE` clause, and are placed after the `SELECT` and `FROM` portions of the query. This structure will look like:

 > **SELECT** *(desired data here)* <br>
 > **FROM** *(table name here)* <br>
 > **WHERE** *(filtering criteria)*

#### 6.2.1) LIMIT command

Here we select the columns `location`, `number_of_households_in_1000` and `number_of_pet_households_in_1000` from the `animals` table. The selection is  are organized by `number_of_pet_households_in_1000` in a descending order. The display is limited to the 10 first rows. 


```python
# Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute("SELECT animals.location, animals.number_of_households_in_1000, animals.number_of_pet_households_in_1000 \
                FROM animals \
                ORDER BY animals.number_of_pet_households_in_1000 \
                DESC \
                LIMIT 10;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]])
```





  <div id="df-b0185b38-fd90-4812-bc62-b3cccc21064e">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>location</th>
      <th>number_of_households_in_1000</th>
      <th>number_of_pet_households_in_1000</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>California</td>
      <td>12974</td>
      <td>6865</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Texas</td>
      <td>9002</td>
      <td>5265</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Florida</td>
      <td>7609</td>
      <td>4138</td>
    </tr>
    <tr>
      <th>3</th>
      <td>New York</td>
      <td>7512</td>
      <td>3802</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Pennsylvania</td>
      <td>5172</td>
      <td>2942</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Ohio</td>
      <td>4661</td>
      <td>2677</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Illinois</td>
      <td>5026</td>
      <td>2602</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Michigan</td>
      <td>3804</td>
      <td>2108</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Georgia</td>
      <td>3798</td>
      <td>2093</td>
    </tr>
    <tr>
      <th>9</th>
      <td>North Carolina</td>
      <td>3701</td>
      <td>2089</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-b0185b38-fd90-4812-bc62-b3cccc21064e')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-b0185b38-fd90-4812-bc62-b3cccc21064e button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-b0185b38-fd90-4812-bc62-b3cccc21064e');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




#### 6.2.2) Multiple WHERE commands

Here we select the columns `location`, `mean_number_of_dogs_per_household` and `mean_number_of_cats` from the `animals` table. The selection considers `mean_number_of_dogs_per_household` and `mean_number_of_cats` equal or greater than 2. The results are organized by `location`. The display is limited to the 3 first rows. 


```python
# Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute("SELECT animals.location, animals.mean_number_of_dogs_per_household, animals.mean_number_of_cats \
                FROM animals \
                WHERE animals.mean_number_of_dogs_per_household >= 2 AND animals.mean_number_of_cats >= 2 \
                ORDER BY animals.location \
                LIMIT 3;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]])
```





  <div id="df-a198e565-97b6-4556-b644-48de5e0a1c5e">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>location</th>
      <th>mean_number_of_dogs_per_household</th>
      <th>mean_number_of_cats</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Arkansas</td>
      <td>2.000</td>
      <td>2.300</td>
    </tr>
    <tr>
      <th>1</th>
      <td>New Mexico</td>
      <td>2.000</td>
      <td>2.200</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Oklahoma</td>
      <td>2.100</td>
      <td>2.200</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-a198e565-97b6-4556-b644-48de5e0a1c5e')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-a198e565-97b6-4556-b644-48de5e0a1c5e button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-a198e565-97b6-4556-b644-48de5e0a1c5e');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




#### 6.2.3) BETWEEN command

Here we select the columns `location`, `mean_number_of_cats` and `cat_population` from the `animals` table. The selection considers `mean_number_of_cats` between 2.2 and 4. The results are organized by `cat_population`. The display is limited to the 3 first rows. 


```python
# Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute("SELECT animals.location, animals.mean_number_of_cats, animals.cat_population \
                FROM animals \
                WHERE animals.mean_number_of_cats \
                BETWEEN 2.2 AND 4 \
                ORDER BY animals.cat_population \
                DESC \
                LIMIT 3;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]])
```





  <div id="df-c5c8659a-e0cc-4c52-be3c-ca8334126e51">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>location</th>
      <th>mean_number_of_cats</th>
      <th>cat_population</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Texas</td>
      <td>2.200</td>
      <td>5565</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Ohio</td>
      <td>2.400</td>
      <td>3786</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Indiana</td>
      <td>2.200</td>
      <td>1912</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-c5c8659a-e0cc-4c52-be3c-ca8334126e51')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-c5c8659a-e0cc-4c52-be3c-ca8334126e51 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-c5c8659a-e0cc-4c52-be3c-ca8334126e51');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




#### 6.2.4) AS command

Here we select the columns `region` together with the sums of `number_of_pet_households_in_1000`, `dog_population_in_1000`,  `cat_population` and the sum of the last two sums from the `animals` table grouped by `region`. These sum variables were named `total_pet_households`, `dog_total`, `cat_total` and `total_companion_pets`.  The results are organized by `total_pet_households` in a descending order. The display is limited to the 3 first rows. 


```python
 # Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute(" SELECT animals.region, SUM(animals.number_of_pet_households_in_1000) AS total_pet_households, SUM(animals.dog_population_in_1000) AS dog_total, \
                 SUM(animals.cat_population) AS cat_total, (SUM(animals.dog_population_in_1000) + SUM(animals.cat_population)) AS total_companion_pets \
                 FROM animals \
                 GROUP BY animals.region \
                 ORDER BY total_pet_households\
                 DESC \
                 LIMIT 3;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]])
```





  <div id="df-8d5ce45e-1412-444b-bf67-c25c9c3fc747">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>region</th>
      <th>total_pet_households</th>
      <th>dog_total</th>
      <th>cat_total</th>
      <th>total_companion_pets</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Central</td>
      <td>18438</td>
      <td>20677</td>
      <td>20332</td>
      <td>41009</td>
    </tr>
    <tr>
      <th>1</th>
      <td>South</td>
      <td>16886</td>
      <td>20253</td>
      <td>18356</td>
      <td>38609</td>
    </tr>
    <tr>
      <th>2</th>
      <td>East</td>
      <td>15991</td>
      <td>13549</td>
      <td>19256</td>
      <td>32805</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-8d5ce45e-1412-444b-bf67-c25c9c3fc747')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-8d5ce45e-1412-444b-bf67-c25c9c3fc747 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-8d5ce45e-1412-444b-bf67-c25c9c3fc747');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




#### 6.2.5) GROUP BY + HAVING command

Here we select the columns `region` and `AVG(percentage_of_households_with_pets)` from the `animals` table grouped by `region`. Notice that we keep the regions with an `AVG(percentage_of_households_with_pets)` greater than 55. **Thus, HAVING is used with the GROUP BY command**. The results are organized by `region` in a descending order. The display is limited to the 3 first rows. 


```python
 # Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute(" SELECT animals.region, AVG(animals.percentage_of_households_with_pets) \
                 FROM animals \
                 GROUP BY animals.region HAVING AVG(animals.percentage_of_households_with_pets) > 55 \
                 ORDER BY animals.region \
                 DESC \
                 LIMIT 3;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]])
```





  <div id="df-97a3bbc0-bb5e-4525-8bed-c8e34743a351">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>region</th>
      <th>avg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>West</td>
      <td>59.9545454545454545</td>
    </tr>
    <tr>
      <th>1</th>
      <td>South</td>
      <td>57.0909090909090909</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Central</td>
      <td>57.0615384615384615</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-97a3bbc0-bb5e-4525-8bed-c8e34743a351')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-97a3bbc0-bb5e-4525-8bed-c8e34743a351 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-97a3bbc0-bb5e-4525-8bed-c8e34743a351');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




#### 6.2.6) Summary

The following summary is provided in the second article that was referenced in part 1:
- `WHERE` provides an opportunity to narrow the criteria for queries.
- Filters may be created with a variety of conditional commands.
- Compound conditional filters may be used in queries.
- `GROUP BY` aggregates information based on categorical dimensions.
- `HAVING` criteria may be applied to the groups that are aggregated.

### 6.3) Aggregating and Grouping Data 

Aggregation SQL functions bring clarity and depth to queries, which include `DISTINCT`, `COUNT`, `GROUP BY` and `HAVING`. These commands add to the filtering accomplished by the `WHERE` clause, and enable viewing data in groups, segments or other organized levels. `DISTINCT` and `COUNT` are often used in the `SELECT` statement to create and quantify aggregation. By contrast, `GROUP BY` and `HAVING` are placed after the `WHERE` clause. As you consider using these tools in your query, it is important to be consistent in the level of aggregation requested in one query. The following shows the appropriate order for the command tools in your new query:

 > **SELECT DISTINCT** *(desired column list)* <br>
 > **FROM** *(table name here)* <br>
 > **WHERE** *(filtering criteria)* <br>
 > **GROUP BY** *(data_name)* **HAVING** *(additional filter)*

#### 6.3.1) DISTINCT, ROUND, COUNT and Multiple GROUP BY commmands

Here we select the columns `item`, `description`,  `qty_sold`,  `avg_transaction_price` and `total_sold` from the `products` table. Notice that the last 3 variables mentioned above refer to `COUNT(sales.item)`, `ROUND(AVG(sales.total),2)` and `avg_transaction_price`. The analysis is limited to unique combinations of items and descriptions. The selected variables were grouped by `item` and then `description`. The results are organized by `qty_sold` in a descending order. The display is limited to the 3 first rows.


```python
 # Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute("SELECT DISTINCT sales.item, sales.description, COUNT(sales.item) as qty_sold, \
                ROUND(AVG(sales.total),2) as avg_transaction_price, SUM(sales.total) as total_sold \
                FROM sales \
                GROUP BY sales.item, sales.description \
                ORDER BY qty_sold \
                DESC \
                LIMIT 3;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]])
```





  <div id="df-5d017793-7c77-4387-ac12-c713b68a1a39">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>item</th>
      <th>description</th>
      <th>qty_sold</th>
      <th>avg_transaction_price</th>
      <th>total_sold</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>11788</td>
      <td>Black Velvet</td>
      <td>31904</td>
      <td>403.19</td>
      <td>12863376.81</td>
    </tr>
    <tr>
      <th>1</th>
      <td>36308</td>
      <td>Hawkeye Vodka</td>
      <td>31105</td>
      <td>172.99</td>
      <td>5380753.20</td>
    </tr>
    <tr>
      <th>2</th>
      <td>43336</td>
      <td>Captain Morgan Original Spiced</td>
      <td>18129</td>
      <td>192.94</td>
      <td>3497803.08</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-5d017793-7c77-4387-ac12-c713b68a1a39')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-5d017793-7c77-4387-ac12-c713b68a1a39 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-5d017793-7c77-4387-ac12-c713b68a1a39');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




Here we select the columns `county`, `qty_sold` and `total_sold` from the `sales` table. Notice that the last 2 variables mentioned above refer to `COUNT(sales.item)` and `SUM(sales.total)`. The analysis is limited to unique county names. Moreover, the description columns must be either 'Black Velvet' or 'Hawkeye Vodka'. The aggregate measure were created by groupbing the table by county if the item count per group exceeded 10000. The results are organized by `total_sold` in a descending order. The display is limited to the 3 first rows.

#### 6.3.2) IN commmand

Here we select the columns `county`, `qty_sold` and `total_sold` from the `products` table. Notice that the last 2 variables mentioned above refer to `COUNT(sales.item)` and `SUM(sales.total)`. The selected variables were grouped by `county` when the count of items was greater than 10000. The results are organized by `total_sold` in a descending order. The display is limited to the 3 first rows.


```python
 # Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute("SELECT DISTINCT sales.county, COUNT(sales.item) as qty_sold, SUM(sales.total) as total_sold \
                FROM sales \
                WHERE sales.description IN('Black Velvet', 'Hawkeye Vodka') \
                GROUP BY sales.county HAVING (COUNT(sales.item)) > 10000 \
                ORDER BY total_sold \
                DESC \
                LIMIT 3;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]])
```





  <div id="df-f8c2692c-de37-4726-b084-fac6e101ab6c">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>county</th>
      <th>qty_sold</th>
      <th>total_sold</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Polk</td>
      <td>28058</td>
      <td>4114463.32</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Linn</td>
      <td>12746</td>
      <td>2366315.91</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-f8c2692c-de37-4726-b084-fac6e101ab6c')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-f8c2692c-de37-4726-b084-fac6e101ab6c button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-f8c2692c-de37-4726-b084-fac6e101ab6c');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




**Notice that you cannot replace (COUNT(sales.item)) by qty_sold** after the HAVING command since it is considered part of the GROUP BY command. Since this command is not "over", the variable qty_sold does not exist yet. However, once the GROUP BY + HAVING commands are used, the variable total_sold can be used in the ORDER BY command.

#### 6.3.3) COUNT(*) and CAST commmands

Here we select the columns `vendor_name`, `products_offered` and `avg_price` from the `products` table. Notice that the last 2 variables mentioned above refer to the count of all instances using `COUNT(*)` and the rounded average of the feature `bottle_price`. The selected variables were grouped by `vendor_name`. The results are organized by `products_offered` in a descending order. The display is limited to the 3 first rows.


```python
 # Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute("SELECT products.vendor_name, COUNT(*) AS products_offered, ROUND(AVG(CAST(products.bottle_price AS DECIMAL)),2) AS avg_price \
                FROM products \
                GROUP BY products.vendor_name \
                ORDER BY products_offered \
                DESC \
                LIMIT 3;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]])
```





  <div id="df-7a19125e-96ee-427a-aac8-8fb79d5b9a4e">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>vendor_name</th>
      <th>products_offered</th>
      <th>avg_price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Jim Beam Brands</td>
      <td>925</td>
      <td>11.54</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Diageo Americas</td>
      <td>906</td>
      <td>18.16</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Pernod Ricard Usa/austin Nichols</td>
      <td>597</td>
      <td>19.80</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-7a19125e-96ee-427a-aac8-8fb79d5b9a4e')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-7a19125e-96ee-427a-aac8-8fb79d5b9a4e button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-7a19125e-96ee-427a-aac8-8fb79d5b9a4e');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




**The CAST command is required so that the bottle price can be use without the monetary sign ($).**

#### 6.3.4) LEFT JOIN + USING commands

Here we select the columns `county`, `population`,  `qty_sold` and `total_sold` from the join of the `sales` and `counties` tables using the `county` column. Notice that the last 2 variables mentioned above refer to `COUNT(sales.item)` and `SUM(sales.total)`. The analysis is limited to unique combinations of counties and population. The selected variables were grouped by `county` and then `population`, when the county population exceeded 150000. The results are organized by `total_sold` in a descending order. The display is limited to the 3 first rows.


```python
 # Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute(" SELECT DISTINCT sales.county, counties.population, COUNT(sales.item) as qty_sold, SUM(sales.total) as total_sold \
                 FROM sales LEFT JOIN counties USING(county) \
                 WHERE description IN('Black Velvet', 'Hawkeye Vodka') \
                 GROUP BY sales.county, counties.population HAVING counties.population > 150000 \
                 ORDER BY total_sold DESC \
                 LIMIT 3;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]])
```





  <div id="df-8de53086-4b04-49a6-be43-13fe7a8c68c2">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>county</th>
      <th>population</th>
      <th>qty_sold</th>
      <th>total_sold</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Polk</td>
      <td>430640</td>
      <td>28058</td>
      <td>4114463.32</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Linn</td>
      <td>211226</td>
      <td>12746</td>
      <td>2366315.91</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Scott</td>
      <td>165224</td>
      <td>5471</td>
      <td>732618.98</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-8de53086-4b04-49a6-be43-13fe7a8c68c2')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-8de53086-4b04-49a6-be43-13fe7a8c68c2 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-8de53086-4b04-49a6-be43-13fe7a8c68c2');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




Notice that there will always be unique combinations of counties and populations since any given county only has one population value. In other types of examples, the constraint of finding unique combinations of variables might be an issue that requires more thought. 

#### 6.3.5) LEFT JOIN + ON commands

Here we have the very same example as the previous query. However, we use `ON` instead of `USING` as a command. 


```python
 # Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute(" SELECT DISTINCT sales.county, counties.population, COUNT(sales.item) as qty_sold, SUM(sales.total) as total_sold \
                 FROM sales LEFT JOIN counties ON sales.county = counties.county \
                 WHERE description IN('Black Velvet', 'Hawkeye Vodka') \
                 GROUP BY sales.county, counties.population \
                 HAVING counties.population > 150000 \
                 ORDER BY total_sold DESC \
                 LIMIT 3;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]])
```





  <div id="df-3dd6693e-8288-4bea-a3a8-0dd32441e518">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>county</th>
      <th>population</th>
      <th>qty_sold</th>
      <th>total_sold</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Polk</td>
      <td>430640</td>
      <td>28058</td>
      <td>4114463.32</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Linn</td>
      <td>211226</td>
      <td>12746</td>
      <td>2366315.91</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Scott</td>
      <td>165224</td>
      <td>5471</td>
      <td>732618.98</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-3dd6693e-8288-4bea-a3a8-0dd32441e518')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-3dd6693e-8288-4bea-a3a8-0dd32441e518 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-3dd6693e-8288-4bea-a3a8-0dd32441e518');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




#### 6.3.6) NOT IN command

Here we select the columns `county`, `qty_sold` and `total_sold` from the `sales` table. Notice that the last 2 variables mentioned above refer to `COUNT(sales.item)` and `SUM(sales.total)`. The analysis is limited to unique combinations of counties considering descriptions that include neither 'Black Velvet' nor 'Hawkeye Vodka' and considering the counties of 'Polk', 'Linn', 'Scott' . The selected variables were grouped by `county`. The results are organized by `total_sold` in a descending order. The display is limited to the 3 first rows.


```python
# Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute(" SELECT DISTINCT sales.county, COUNT(sales.item) as qty_sold, SUM(sales.total) as total_sold \
                 FROM sales \
                 WHERE sales.description NOT IN('Black Velvet','Hawkeye Vodka') \
                 AND sales.county IN('Polk', 'Linn', 'Scott') \
                 GROUP BY sales.county \
                 ORDER BY total_sold \
                 DESC \
                 LIMIT 3;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]])
```





  <div id="df-9dcc8738-518c-44e1-b65e-d6b02641454c">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>county</th>
      <th>qty_sold</th>
      <th>total_sold</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Polk</td>
      <td>533688</td>
      <td>82282998.47</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Linn</td>
      <td>238524</td>
      <td>32093731.58</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Scott</td>
      <td>182849</td>
      <td>27170229.69</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-9dcc8738-518c-44e1-b65e-d6b02641454c')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-9dcc8738-518c-44e1-b65e-d6b02641454c button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-9dcc8738-518c-44e1-b65e-d6b02641454c');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




#### 6.3.7) Summary

The following summary is provided in the second article that was referenced in part 1:
- `DISTINCT` and `GROUP BY` provide insights into aggregated slices of the data.
- `COUNTing` the quantity of a group member can provide valuable insight.
- Compound conditions can be joined by `AND` in queries.
- `HAVING` further filters aggregated data, in addition to filtering applied to raw data by the `WHERE` clause.

### 6.4) JOIN command

Just as we organize belongings into separate storage areas, data needed for analysis is often stored in multiple locations. SQL enables you to easily combine data from multiple resources, if you have a unique identifier to bridge between the data tables. Connecting data sources is established with the `FROM` clause, identifying the source tables and the fields which are candidates for the unique connection. We’ll begin with the `INNER JOIN`, which returns only the records that match exactly from both tables. The basic command frame for the `SELECT` statement remains the same, with some new additions. When referencing columns within a `JOIN` query, use the formal labeling for column names, meaning table name together with column name separated by a period, for clear identification. The structure to `JOIN` two tables within the `FROM` clause is accomplished as follows:

 > **FROM** *(primary table name here)* <br>
 > **INNER JOIN** *(secondary table here)* <br>
 > **ON** *(primary_table.field_from_primary = secondary_table.field_from_secondary)*

#### 6.4.1) INNER JOIN command

Here we select the columns `store_number`, `name`, `store_address` and `total_sold` from the join of the `sales2016` and `stores` tables. Notice that the last variable mentioned above refers to `SUM(sales2016.sale_dollars)`.  The `INNER JOIN` command used the columns `store_number` and `store` to merge the tables. The selected variables were grouped by `store_number`, `name` and `store_address`. The results are organized by `total_sold` in a descending order. The display is limited to the 3 first rows.


```python
 # Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute("SELECT sales_2016.store_number, stores.name, stores.store_address, SUM(sales_2016.sale_dollars) AS total_sold \
                FROM sales_2016 \
                INNER JOIN stores ON sales_2016.store_number = stores.store \
                GROUP BY sales_2016.store_number, stores.name, stores.store_address \
                ORDER BY total_sold \
                DESC \
                LIMIT 3;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]])
```





  <div id="df-ebba77fe-04e6-4fc4-ab3f-40cd39a1ff11">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>store_number</th>
      <th>name</th>
      <th>store_address</th>
      <th>total_sold</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2633</td>
      <td>Hy-vee #3 / Bdi / Des Moines</td>
      <td>3221 Se 14th St\nDes Moines, IA 503200000\n(41...</td>
      <td>7904425.39</td>
    </tr>
    <tr>
      <th>1</th>
      <td>4829</td>
      <td>Central City 2</td>
      <td>1501 Michigan Ave\nDes Moines, IA 50314\n(41.6...</td>
      <td>7156755.00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2512</td>
      <td>Hy-vee Wine and Spirits / Iowa City</td>
      <td>1720 Waterfront Dr\nIowa City, IA 522400000\n(...</td>
      <td>3400203.01</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-ebba77fe-04e6-4fc4-ab3f-40cd39a1ff11')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-ebba77fe-04e6-4fc4-ab3f-40cd39a1ff11 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-ebba77fe-04e6-4fc4-ab3f-40cd39a1ff11');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




#### 6.4.2) Divide one column by another (feature creation)

Here we select the columns `county`, `total_sold`, `population` and `per_capita_spend` from the join of the `sales2016` and `counties` tables. Notice that the the variables `total_sold` and `per_capita_spend` mentioned above refer to `SUM(sales2016.sale_dollars)` and `ROUND((SUM(sales2016.sale_dollars)/(counties.population)),2)`.  The `INNER JOIN` command used the column `county` to merge the tables. The selected variables were grouped by `county` and `population`. The results are organized by `per_capita_spend` in a descending order. The display is limited to the 3 first rows.


```python
 # Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute("SELECT sales_2016.county, SUM(sales_2016.sale_dollars) AS total_sold, counties.population, \
                ROUND((SUM(sales_2016.sale_dollars)/(counties.population)),2) AS per_capita_spend \
                FROM sales_2016 \
                INNER JOIN counties USING(county) \
                GROUP BY sales_2016.county, counties.population \
                ORDER BY per_capita_spend \
                DESC \
                LIMIT 3;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]])
```





  <div id="df-ec9e1df7-559a-4f7c-93e0-222f27dd4e39">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>county</th>
      <th>total_sold</th>
      <th>population</th>
      <th>per_capita_spend</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Dickinson</td>
      <td>3112712.41</td>
      <td>16667</td>
      <td>186.76</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Polk</td>
      <td>42400328.31</td>
      <td>430640</td>
      <td>98.46</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Cerro Gordo</td>
      <td>3617023.05</td>
      <td>44151</td>
      <td>81.92</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-ec9e1df7-559a-4f7c-93e0-222f27dd4e39')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-ec9e1df7-559a-4f7c-93e0-222f27dd4e39 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-ec9e1df7-559a-4f7c-93e0-222f27dd4e39');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




#### 6.4.3) COUNT + DISTINCT commands

Here we select the columns `county`, `qty_stores`, `total_sales`, `county_population` and `num_people_per_store` from the join of the `sales_2016` and `counties` tables. Notice that the the variables `qty_stores`, `total_sales` and `num_people_per_store` mentioned above refer to `COUNT(DISTINCT sales_2016.store_number)`, `SUM(sales_2016.sale_dollars)` and `(counties.population/(COUNT(DISTINCT sales_2016.store_number)))`.  The `INNER JOIN` command used the column `county` to merge the tables. Notice that the selection is limited to the counties of 'Dickinson', 'Polk', 'Johnson' and 'Cerro Gordo'. The selected variables were grouped by `county` and then `population`. The results are organized by `total_sales` in a descending order. The display is limited to the 3 first rows.


```python
 # Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute("SELECT sales_2016.county, COUNT(DISTINCT sales_2016.store_number) AS qty_stores, SUM(sales_2016.sale_dollars) AS total_sales, \
                counties.population AS county_population, (counties.population/(COUNT(DISTINCT sales_2016.store_number))) AS num_people_per_store \
                FROM sales_2016 INNER JOIN counties USING(county) \
                WHERE sales_2016.county IN('Dickinson', 'Polk', 'Johnson', 'Cerro Gordo' ) \
                GROUP BY sales_2016.county, counties.population \
                ORDER BY total_sales DESC \
                LIMIT 3;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]])
```





  <div id="df-749b35d6-0e52-4b0a-bb32-9b405ee63947">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>county</th>
      <th>qty_stores</th>
      <th>total_sales</th>
      <th>county_population</th>
      <th>num_people_per_store</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Polk</td>
      <td>188</td>
      <td>42400328.31</td>
      <td>430640</td>
      <td>2290</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Johnson</td>
      <td>50</td>
      <td>10509392.26</td>
      <td>130882</td>
      <td>2617</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Cerro Gordo</td>
      <td>20</td>
      <td>3617023.05</td>
      <td>44151</td>
      <td>2207</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-749b35d6-0e52-4b0a-bb32-9b405ee63947')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-749b35d6-0e52-4b0a-bb32-9b405ee63947 button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-749b35d6-0e52-4b0a-bb32-9b405ee63947');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




#### 6.4.4) IS NOT NULL command

Here we select the columns `county`, `store_number`, `store_name`, `qty_sold`, `avg_sale_price` and `total_sold` from the join of the `sales_2016` and `convenience` tables. Notice that the the 3 lasts variables mentioned above refer to `COUNT(sales_2016.sale_dollars)`, `ROUND(AVG(sales_2016.sale_dollars),2)` and `SUM(sales_2016.sale_dollars)`.  The `INNER JOIN` command used the columns `store_numbers` and `store` to merge the tables. Notice that the selection is limited stores values that are not NULL and to the counties of 'Dickinson', 'Polk' and 'Johnson'. The selected variables were grouped by `county`, `store_number` and `store_names`. The results are organized by `county` and then `total_sold` in a descending order. The display is limited to the 3 first rows.


```python
 # Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute(" SELECT sales_2016.county, sales_2016.store_number, sales_2016.store_name, COUNT(sales_2016.sale_dollars) AS qty_sold, \
                 ROUND(AVG(sales_2016.sale_dollars),2) AS avg_sale_price, SUM(sales_2016.sale_dollars) AS total_sold \
                 FROM sales_2016 \
                 INNER JOIN convenience ON sales_2016.store_number = convenience.store \
                 WHERE convenience.store IS NOT NULL AND sales_2016.county IN('Johnson','Dickinson', 'Polk') \
                 GROUP BY sales_2016.county, sales_2016.store_number, sales_2016.store_name \
                 ORDER BY sales_2016.county, total_sold \
                 DESC \
                 LIMIT 3;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]])
```





  <div id="df-c0788904-8f01-455f-baac-bfef2d8688ef">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>county</th>
      <th>store_number</th>
      <th>store_name</th>
      <th>qty_sold</th>
      <th>avg_sale_price</th>
      <th>total_sold</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Dickinson</td>
      <td>4576</td>
      <td>THE BOONEDOCKS</td>
      <td>823</td>
      <td>92.26</td>
      <td>75929.34</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Dickinson</td>
      <td>4582</td>
      <td>Pronto / Spirit Lake</td>
      <td>634</td>
      <td>89.43</td>
      <td>56698.15</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Dickinson</td>
      <td>4387</td>
      <td>KUM &amp; GO #117 / SPIRIT LAKE</td>
      <td>293</td>
      <td>99.13</td>
      <td>29045.78</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-c0788904-8f01-455f-baac-bfef2d8688ef')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-c0788904-8f01-455f-baac-bfef2d8688ef button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-c0788904-8f01-455f-baac-bfef2d8688ef');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




#### 6.4.5) CASE + COALESCE commands


We begin the query by considering the column store_address. We get the position of the term "IA". Then, we consider that if the position 3 string to the right of IA plus 1 position is smaller than '1', then this value should be considered as 'no zip'. Notice that this statement is useful in the case of "Dehner Distillery" where no zip code is available. Instead, in this case, we have "\n" following the word IA. Since "\n" is smaller than "1", then we have 'no zip' associated with this situation. In other cases, such as when the name is 'Louisiana Spirits LLC', then there is a zip number. This number goes from 3 positions to the right of "IA" up until (3 + 5) positions to the right of "IA". In usch circumstances, the value extracted is the zip code number. These values are saved in column called zipcode. <br>

Other columns are also selected. including `name`, `store_id` and `store_status`. Another piece of information is also extracted from the store_address column by considering the first string up until 1 position to the right of "IA". This piece of information corresponds to the street address. `COALESCE` is used to get the first non-null argument. <br>

Here we selection is made from the join of the `stores` and `sales` tables. The `LEFT JOIN` command used the column `store`. Notice that the selection is limited to instances with store_status equal to "A" and total sales equal to null. The results are organized by `zipcode` in a descending order. The display is limited to the 3 first rows.


```python
 # Connect to the table in the database
connection = psycopg2.connect(user = 'postgres', password = 'postgres', host = 'localhost', database = 'tfio_demo')
cursor = connection.cursor()

# Create SQL query
cursor.execute(" SELECT CASE WHEN SUBSTRING(store_address,(POSITION('IA' in stores.store_address)+3),1) < '1' THEN 'no zip' \
                 ELSE SUBSTRING(store_address,(POSITION('IA' in stores.store_address)+3),5) END AS zipcode, \
                 stores.name, stores.store AS store_id, store_status, \
                 SUBSTRING(store_address,1,(POSITION('IA' in stores.store_address)+1)) AS st_address, \
                 COALESCE(sales.total, 0) AS sales_totals \
                 FROM stores LEFT JOIN sales USING (store) \
                 WHERE store_status = 'A' AND sales.total IS NULL \
                 ORDER BY zipcode \
                 DESC \
                 LIMIT 3;")
table_contacts = cursor.fetchall()

# Turn the results of the query into a dataframe for visualization of the results
pd.DataFrame((table_contacts) , columns=[[desc[0] for desc in cursor.description]])
```





  <div id="df-09d5859f-fee4-4017-a8eb-882ef114eb9d">
    <div class="colab-df-container">
      <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>zipcode</th>
      <th>name</th>
      <th>store_id</th>
      <th>store_status</th>
      <th>st_address</th>
      <th>sales_totals</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>no zip</td>
      <td>Dehner Distillery</td>
      <td>9919</td>
      <td>A</td>
      <td>7500, University Ave\nClive, IA</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>70650</td>
      <td>Louisiana Spirits LLC</td>
      <td>9920</td>
      <td>A</td>
      <td>20909, South I-10 Frontage Rd\nLacassine, IA</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>52804</td>
      <td>Sub Xpress &amp; Gas</td>
      <td>4526</td>
      <td>A</td>
      <td>4307 W Locust St\nDavenport, IA</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>
      <button class="colab-df-convert" onclick="convertToInteractive('df-09d5859f-fee4-4017-a8eb-882ef114eb9d')"
              title="Convert this dataframe to an interactive table."
              style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M0 0h24v24H0V0z" fill="none"/>
    <path d="M18.56 5.44l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94zm-11 1L8.5 8.5l.94-2.06 2.06-.94-2.06-.94L8.5 2.5l-.94 2.06-2.06.94zm10 10l.94 2.06.94-2.06 2.06-.94-2.06-.94-.94-2.06-.94 2.06-2.06.94z"/><path d="M17.41 7.96l-1.37-1.37c-.4-.4-.92-.59-1.43-.59-.52 0-1.04.2-1.43.59L10.3 9.45l-7.72 7.72c-.78.78-.78 2.05 0 2.83L4 21.41c.39.39.9.59 1.41.59.51 0 1.02-.2 1.41-.59l7.78-7.78 2.81-2.81c.8-.78.8-2.07 0-2.86zM5.41 20L4 18.59l7.72-7.72 1.47 1.35L5.41 20z"/>
  </svg>
      </button>

  <style>
    .colab-df-container {
      display:flex;
      flex-wrap:wrap;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

      <script>
        const buttonEl =
          document.querySelector('#df-09d5859f-fee4-4017-a8eb-882ef114eb9d button.colab-df-convert');
        buttonEl.style.display =
          google.colab.kernel.accessAllowed ? 'block' : 'none';

        async function convertToInteractive(key) {
          const element = document.querySelector('#df-09d5859f-fee4-4017-a8eb-882ef114eb9d');
          const dataTable =
            await google.colab.kernel.invokeFunction('convertToInteractive',
                                                     [key], {});
          if (!dataTable) return;

          const docLinkHtml = 'Like what you see? Visit the ' +
            '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
            + ' to learn more about interactive tables.';
          element.innerHTML = '';
          dataTable['output_type'] = 'display_data';
          await google.colab.output.renderOutput(dataTable, element);
          const docLink = document.createElement('div');
          docLink.innerHTML = docLinkHtml;
          element.appendChild(docLink);
        }
      </script>
    </div>
  </div>




The following is the sytax for using CASE: 

> **CASE** <br>
> &emsp; **WHEN** *(condition_1)*  **THEN** *(result_1)* <br>
> &emsp; **WHEN** *(condition_2)*  **THEN** *(result_2)* <br>
> &emsp; [**WHEN** ...] <br>
> &emsp; [**ELSE** else_result] <br>
> **END**

#### 6.4.6) Summary

The following summary is provided in the second article that was referenced in part 1:
- `INNER JOIN` is the most common joining type, and is the default assumed, if you only include the command word `JOIN` in your query.
- `ON` is the phrase to specify the matching unique identifier in both tables.
- If the matching key columns in both tables have exactly matching names USING(column_name) may be used instead of the `ON` command structure.
- When combining tables, the column or field names must include a way for SQL to correctly identify their source table. This is accomplished by providing the full table name or assigning an alias.
- Appropriate use of `WHERE` clause filters can enable queries to narrow the focus of the inquiry and search rapidly through very large datasets quickly containing millions of records.
- As shown in the final query, there are many tools for manipulating text fields and including specific parts in your reports as you answer stakeholder’s questions. Some of these essential tools are `SUBSTRING`, `POSITION`, `COALESCE` and `CASE` statements, which are all coming in the next sections.
