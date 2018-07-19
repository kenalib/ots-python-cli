
# Table Store コマンド (OTS CLI) の使い方


## インストールと Table Store へのアクセス

* 次のページから otscli_V1.1.zip をダウンロードして解凍
* https://jp.alibabacloud.com/help/doc-detail/34160.htm
* url はコンソール内でインスタンス作成後、その詳細画面で確認できる

```bash
cd otscli_V1.1/otscli
python onekey_INSTALL.py
python ots_console \
    --url http://my-table-store.ap-southeast-1.ots.aliyuncs.com \
    --id xxxxx --key xxxxxxxxxx
OTS-my-table-store>:
# interactive shell 開始
```

## Create table, insert update and delete

* 主 key を指定してテーブル作成、及び、データ操作
* readrt: Reserved Read Throughput
* writert: Reserved Write Throughput

```bash
# CREATE TABLE
>: ct test_table1 k1:string,k2:string readrt:100 writert:50
>: lt               # list tables
>: ti test_table1   # table info

# INSERT
>: put test_table1 s4,s5 height:double='1.65',weight:double='58.5'
>: put test_table1 s5,s6 age:integer='30'
>: get test_table1 s5,s6
age:INTEGER='30'

# SELECT (range all)
>: getrange test_table1 s1,s2 s4,s5
>: getrange test_table1 INF_MIN INF_MAX

# UPDATE
>: update test_table1 s5,s6 age:integer='29'
>: get test_table1 s5,s6
age:INTEGER='29'

# DELETE
>: delete test_table1 s5,s6
>: get test_table1 s5,s6
# Invalid primary key.

# DROP TABLE
dt test_table1
```

## Import/Export and delete table

* テーブルのデータをテキストファイルにエクスポート・インポートできる

```
>: export test_table1 /tmp/tabledata.txt
>: import test_table1 /tmp/tabledata.txt

>: dt test_table1
```


## Run Batch

* 処理内容を書いたファイルを作成 (例: lt)
* そのファイル内容を `--c` 内で展開する

```bash
echo lt > ots_batch.bat

python ots_console \
    --url http://my-table-store.ap-southeast-1.ots.aliyuncs.com \
    --id xxxxx --key xxxxxxxxxx
    --i false --c $(cat ots_batch.bat)
```

## Reference 

* Table Store コマンドプロンプト https://jp.alibabacloud.com/help/doc-detail/34160.htm
