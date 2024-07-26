# Trino and Iceberg REST Catalog Demo

## Start containers

```bash
docker compose up -d
```

This will initialize `iceberg` catalog in Trino.

## Initialize `test` schema

There is a simple SQL script to initialize a test schema
in the `iceberg` catalog by copying TPCH "tiny" schema from the Trino:

```bash
docker exec -it trino trino -f /home/trino/test-schema.sql
```

This is not required. It is possible to create other schemata in the `iceberg`
catalog and create and populate tables there in any way.

## Running queries

Connect to Trino CLI and execute queries:

```bash
docker exec -it trino trino
```

Inspect warehouse bucket contents: open [Minio Admin panel](http://localhost:9001)
(user name: `admin` password: `password`).

```bash
SHOW SCHEMAS from iceberg;
```

```bash
SHOW TABLES from iceberg.test;
```

```bash
select * from iceberg.test.customer;
```

```bash
CREATE SCHEMA iceberg.demo;
```

```bash
CREATE TABLE iceberg.demo.taxis (
  vendor_id bigint,
  trip_id bigint,
  trip_distance real,
  fare_amount double,
  store_and_fwd_flag varchar
)
WITH (
  partitioning = ARRAY['vendor_id']
);
```

```bash
INSERT INTO iceberg.demo.taxis
VALUES (1, 1000371, 1.8, 15.32, 'N'), (2, 1000372, 2.5, 22.15, 'N'), (2, 1000373, 0.9, 9.01, 'N'), (1, 1000374, 8.4, 42.13, 'Y');
```

```bash
SELECT * FROM iceberg.demo.taxis;
```
