# phpMyAdminの覚書2

## 概要

- MySQLのテーブルデータのリレーションシップやJOIN(結合)によるデータ取得のおさえるためのハンズオン演習です。  
  データベース上にリレーションシップをもつテーブルを作成し、適切な表題をもつデータの取得について演習しました。

### テーブルの作成（customersテーブル、productsテーブル、ordersテーブル）

- 

### テーブルのデータ挿入、主キーと外部キーのリレーションシップ

- 

### リレーションシップを持つテーブルのデータ取得 

- すべての注文情報を取得（カラムは顧客名、商品名、数量、注文日で表示）

'''
SELECT 
    c.name AS 顧客名, -- customersテーブルのnameカラムと顧客名で表示
    p.product_name AS 商品名, -- productsテーブルのnameカラムを商品名と表示
    o.quantity AS 数量, -- ordersテーブルのquantityカラムを数量と表示
    o.order_date AS 注文日 -- ordersテーブルのorder_dateカラムを注文日と表示
FROM 
    orders AS o -- ordersテーブルをoとする
INNER JOIN 
    customers AS c ON o.customer_id = c.customer_id -- ordersテーブルとcustomersテーブルのcustomer_idのデータ結合
INNER JOIN 
    products AS p ON o.product_id = p.product_id; -- ordersテーブルとproductsテーブルのproduct_idのデータ結合
'''

(参考図)
<img width="1212" height="1491" alt="Image" src="https://github.com/user-attachments/assets/15ac1101-193d-472a-8cbd-408b9bad0465" />
