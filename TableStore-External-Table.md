
# MaxCompute から Table Store 上のデータにアクセスする方法


## Table Store 上で CREATE TABLE

* コンソール上で Instance を事前に作成、otscli でアクセスする
* テーブル名は ots_tpch_orders、主キーは o_orderkey, o_orderdate
* ここではテストデータを１件作成する

```bash
ct ots_tpch_orders o_orderkey:integer,o_orderdate:string readrt:100 writert:50
put ots_tpch_orders 123,20180710 o_custkey:integer=567,o_orderstatus:string=created,o_totalprice:double=1500.5
```

## MaxCompute 上で CREATE EXTERNAL TABLE

```sql
CREATE EXTERNAL TABLE IF NOT EXISTS ots_table_external (
    odps_orderkey bigint,
    odps_orderdate string,
    odps_custkey bigint,
    odps_orderstatus string,
    odps_totalprice double
)
STORED BY 'com.aliyun.odps.TableStoreStorageHandler'
WITH SERDEPROPERTIES (
    'tablestore.columns.mapping'=':o_orderkey,:o_orderdate,o_custkey,o_orderstatus,o_totalprice',
    'tablestore.table.name'='ots_tpch_orders'
)
LOCATION 'tablestore://xxxxx:xxxxxxxxxx@my-table-store.ap-southeast-1.ots.aliyuncs.com';
```

## MaxCompute 上で SELECT

```sql
select * from ots_table_external;
+---------------+----------------+--------------+------------------+-----------------+
| odps_orderkey | odps_orderdate | odps_custkey | odps_orderstatus | odps_totalprice | 
+---------------+----------------+--------------+------------------+-----------------+
| 123           | 20180710       | 567          | created          | 1500.5          | 
+---------------+----------------+--------------+------------------+-----------------+
1 records (at most 10000 supported) fetched by instance tunnel.
```

## Reference

* Table Store データへのアクセス https://jp.alibabacloud.com/help/doc-detail/54519.htm
