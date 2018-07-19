
# Chicago Crime Data を Table Store に準備する

いわゆる Chicago Crime Data を Table Store に入れてみる

## Chicago Crime Data とは

広く一般に知られた公開された、ビッグデータ分析のサンプルデータの一つ

```
ID,Case Number,Date,Block,IUCR,Primary Type,Description,Location Description,Arrest,Domestic,Beat,District,Ward,Community Area,FBI Code,X Coordinate,Y Coordinate,Year,Updated On,Latitude,Longitude,Location
5059066,HM664618,10/17/2006 01:30:00 AM,045XX W VAN BUREN ST,0486,BATTERY,DOMESTIC BATTERY SIMPLE,APARTMENT,false,true,1131,011,24,26,08B,1146219,1897639,2006,04/15/2016 08:55:02 AM,41.875103951,-87.738611562,"(41.875103951, -87.738611562)"
5059067,HM666703,10/18/2006 02:00:00 AM,053XX S CORNELL AVE,1310,CRIMINAL DAMAGE,TO PROPERTY,APARTMENT,false,false,2132,002,5,41,14,1188158,1870244,2006,04/15/2016 08:55:02 AM,41.799030006,-87.585505972,"(41.799030006, -87.585505972)"
5059069,HM666410,10/17/2006 09:45:00 PM,070XX S JEFFERY BLVD,0580,STALKING,SIMPLE,RESIDENCE PORCH/HALLWAY,true,false,0331,003,5,43,08A,1190768,1858499,2006,04/15/2016 08:55:02 AM,41.766738169,-87.576313866,"(41.766738169, -87.576313866)"
5059072,HM666019,10/17/2006 06:00:00 PM,034XX W WRIGHTWOOD AVE,0430,BATTERY,AGGRAVATED: OTHER DANG WEAPON,SIDEWALK,false,false,1412,014,35,22,04B,1152822,1917070,2006,04/15/2016 08:55:02 AM,41.928296353,-87.713852667,"(41.928296353, -87.713852667)"
5059075,HM664823,10/17/2006 07:40:00 AM,122XX S PEORIA ST,1310,CRIMINAL DAMAGE,TO PROPERTY,RESIDENCE,false,true,0524,005,34,53,14,1172545,1823597,2006,04/15/2016 08:55:02 AM,41.671382767,-87.644131946,"(41.671382767, -87.644131946)"
```

* https://data.cityofchicago.org/Public-Safety/Crimes-2001-to-present/ijzp-q8t2
* https://cloud.google.com/bigquery/public-data/chicago-crime-data

## Chicago Crime テーブル作成

元のデータは巨大なので、head コマンドで一旦小さなデータを作成する。
カラム数は22、主キーはID、このテーブルを Table Store 上に作成。

前回記事のサンプルコマンドによると o_ をカラム名に付けるようで、
ここでも、このコンベンションに従うこととする。

```bash
# CREATE TABLE
ct ots_chicago_crime o_ID:integer readrt:100 writert:50
```

## データ作成

1行ごとにデータ作成する場合は、以下のコマンドを利用。
カラム名は先頭に `o_` を付与し、空白は `_` に置き換えている。

また、負数を double で入れようとすると怒られるので、
止むを得ず、元のデータの `-87` は `87` に置き換えている。

```
put ots_chicago_crime 5059066 o_X_Coordinate:INTEGER='1146219',o_FBI_Code:STRING='08B',o_Arrest:BOOLEAN='False',o_Y_Coordinate:INTEGER='1897639',o_Case_Number:STRING='HM664618',o_Domestic:BOOLEAN='True',o_Longitude:DOUBLE='87.738611562',o_Primary_Type:STRING='BATTERY',o_Beat:STRING='1131',o_Updated_On:STRING='04/15/2016 08:55:02 AM',o_District:STRING='011',o_Date:STRING='10/17/2006 01:30:00 AM',o_Latitude:DOUBLE='41.875103951',o_Location_Description:STRING='APARTMENT',o_Description:STRING='DOMESTIC BATTERY SIMPLE',o_Block:STRING='045XX W VAN BUREN ST',o_Community_Area:STRING='26',o_Location:STRING='(41.875103951, -87.738611562)',o_Year:INTEGER='2006',o_Ward:STRING='24',o_IUCR:STRING='0486'
```

## インポート用のデータファイルを作成

個々の行のデータを put していくのは、otscli での記号の取り扱いに難があり、
また、長い時間がかかるので、import コマンドでバルク `import` を目指す。
以下に3行目までを例示。以下同様に必要な行を作成する。

```
o_ID:INTEGER='5059066' o_X_Coordinate:INTEGER='1146219',o_FBI_Code:STRING='08B',o_Arrest:BOOLEAN='False',o_Y_Coordinate:INTEGER='1897639',o_Case_Number:STRING='HM664618',o_Domestic:BOOLEAN='True',o_Longitude:DOUBLE='87.738611562',o_Primary_Type:STRING='BATTERY',o_Beat:STRING='1131',o_Updated_On:STRING='04/15/2016 08:55:02 AM',o_District:STRING='011',o_Date:STRING='10/17/2006 01:30:00 AM',o_Latitude:DOUBLE='41.875103951',o_Location_Description:STRING='APARTMENT',o_Description:STRING='DOMESTIC BATTERY SIMPLE',o_Block:STRING='045XX W VAN BUREN ST',o_Community_Area:STRING='26',o_Location:STRING='(41.875103951, -87.738611562)',o_Year:INTEGER='2006',o_Ward:STRING='24',o_IUCR:STRING='0486'
o_ID:INTEGER='5059067' o_X_Coordinate:INTEGER='1188158',o_FBI_Code:STRING='14',o_Arrest:BOOLEAN='False',o_Y_Coordinate:INTEGER='1870244',o_Case_Number:STRING='HM666703',o_Domestic:BOOLEAN='False',o_Longitude:DOUBLE='87.585505972',o_Primary_Type:STRING='CRIMINAL DAMAGE',o_Beat:STRING='2132',o_Updated_On:STRING='04/15/2016 08:55:02 AM',o_District:STRING='002',o_Date:STRING='10/18/2006 02:00:00 AM',o_Latitude:DOUBLE='41.799030006',o_Location_Description:STRING='APARTMENT',o_Description:STRING='TO PROPERTY',o_Block:STRING='053XX S CORNELL AVE',o_Community_Area:STRING='41',o_Location:STRING='(41.799030006, -87.585505972)',o_Year:INTEGER='2006',o_Ward:STRING='5',o_IUCR:STRING='1310'
o_ID:INTEGER='5059069' o_X_Coordinate:INTEGER='1190768',o_FBI_Code:STRING='08A',o_Arrest:BOOLEAN='True',o_Y_Coordinate:INTEGER='1858499',o_Case_Number:STRING='HM666410',o_Domestic:BOOLEAN='False',o_Longitude:DOUBLE='87.576313866',o_Primary_Type:STRING='STALKING',o_Beat:STRING='0331',o_Updated_On:STRING='04/15/2016 08:55:02 AM',o_District:STRING='003',o_Date:STRING='10/17/2006 09:45:00 PM',o_Latitude:DOUBLE='41.766738169',o_Location_Description:STRING='RESIDENCE PORCH/HALLWAY',o_Description:STRING='SIMPLE',o_Block:STRING='070XX S JEFFERY BLVD',o_Community_Area:STRING='43',o_Location:STRING='(41.766738169, -87.576313866)',o_Year:INTEGER='2006',o_Ward:STRING='5',o_IUCR:STRING='0580'
```

これを `tablecrime.txt` という名前で適宜保存

## データのインポート

必要な行のデータが完成したら、`otscli` からインポート。

```
>: import ots_chicago_crime /tmp/tablecrime_head.txt
```


## Next...

MaxCompute の Hive 経由でクエリして、速さを見てみる。
