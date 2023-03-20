## mysql和postgresql join
```
CREATE TABLE products (
    id INT,
    name STRING,
    description STRING,
    PRIMARY KEY (id) NOT ENFORCED
  ) WITH (
    'connector' = 'mysql-cdc',
    'hostname' = 'mysql_server',
    'port' = '3306',
    'username' = 'liulu17',
    'password' = '562919ll',
    'database-name' = 'flink',
    'table-name' = 'products'
  );


  CREATE TABLE orders (
   order_id INT,
   order_date TIMESTAMP(0),
   customer_name STRING,
   price DECIMAL(10, 5),
   product_id INT,
   order_status BOOLEAN,
   PRIMARY KEY (order_id) NOT ENFORCED
 ) WITH (
   'connector' = 'mysql-cdc',
   'hostname' = 'mysql_server',
   'port' = '3306',
   'username' = 'liulu17',
   'password' = '562919ll',
   'database-name' = 'flink',
   'table-name' = 'orders'
 );




 CREATE TABLE shipments (
   shipment_id INT,
   order_id INT,
   origin STRING,
   destination STRING,
   is_arrived BOOLEAN,
   PRIMARY KEY (shipment_id) NOT ENFORCED
 ) WITH (
   'connector' = 'postgres-cdc',
   'hostname' = 'postgres_server',
   'port' = '5432',
   'username' = 'postgres',
   'password' = '562919ll',
   'database-name' = 'postgres',
   'decoding.plugin.name' = 'pgoutput',
   'schema-name' = 'public',
   'table-name' = 'shipments'
 );
 ```