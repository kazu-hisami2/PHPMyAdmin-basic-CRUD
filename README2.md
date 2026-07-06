# phpMyAdminによるデータベース操作の覚書2

## 概要

- MySQLのテーブルデータのリレーションシップやJOIN(結合)によるデータ取得のおさえるためのハンズオン演習です。  
  データベース上にリレーションシップをもつテーブルを作成し、適切な表題をもつデータの取得について演習しました。

### テーブルの作成（customersテーブル、productsテーブル、ordersテーブル）

- テーブル1: customers（顧客）
```
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(150) UNIQUE NOT NULL,
    phone VARCHAR(20)
);
```

- テーブル2: products（商品）
```
CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    product_name VARCHAR(200) NOT NULL,
    price INT NOT NULL
);
```
- テーブル3: orders（注文)
```
CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    order_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

### サンプルデータの挿入

- customers:
```
INSERT INTO customers (name, email, phone) VALUES
('田中太郎', 'tanaka@example.com', '090-1234-5678'),
('佐藤花子', 'sato@example.com', '080-2345-6789'),
('鈴木一郎', 'suzuki@example.com', '070-3456-7890');
```

- products:
```
INSERT INTO products (product_name, price) VALUES
('ノートPC', 120000),
('マウス', 2000),
('キーボード', 5000),
('モニター', 30000),
('ヘッドフォン', 8000);
```

- orders:
```
INSERT INTO orders (customer_id, product_id, quantity) VALUES
(1, 1, 1),  -- 田中太郎がノートPCを1台
(1, 2, 2),  -- 田中太郎がマウスを2個
(2, 3, 1),  -- 佐藤花子がキーボードを1個
(3, 1, 1),  -- 鈴木一郎がノートPCを1台
(3, 4, 2);  -- 鈴木一郎がモニターを2台
```

(関係図)
<img width="1526" height="1026" alt="Image" src="https://github.com/user-attachments/assets/00ce1f79-806b-4ef3-a796-c9c97e735234" />
このように、customersテーブルとproductsテーブルはordersテーブルに1対多の関係となる

### リレーションシップを持つテーブルのデータ取得 

- すべての注文情報を取得（カラムは顧客名、商品名、数量、注文日で表示）

```
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
```

(参考図)
<img width="1212" height="1491" alt="Image" src="https://github.com/user-attachments/assets/15ac1101-193d-472a-8cbd-408b9bad0465" />

- 田中太郎さんの注文履歴を取得（カラムは商品名と数量で表示）

・'田中太郎'の名前でデータ取得する場合(ordersテーブルとcustomersテーブルのデータ結合が必要)
```
SELECT   
    p.product_name AS 商品名, -- productsテーブルのnameカラムを商品名と表示
    o.quantity AS 数量 -- ordersテーブルのquantityカラムを数量と表示
FROM
    orders AS o -- ordersテーブルをoとする
INNER JOIN
    customers AS c ON o.customer_id = c.customer_id -- ordersテーブルとcustomersテーブルのcustomer_idのデータ結合
INNER JOIN
    products AS p ON o.product_id = p.product_id -- ordersテーブルとproductsテーブルのproduct_idのデータ結合
WHERE
    c.id = '田中太郎'; -- customersテーブルに'田中太郎'を含む項目の部分
```

・'田中太郎'さんのcustomer_id(=1)がわかっているときにデータ取得する場合(ordersテーブルとcustomersテーブルのデータ結合は不要)
```
SELECT   
    p.product_name AS 商品名, -- productsテーブルのnameカラムを商品名と表示
    o.quantity AS 数量 -- ordersテーブルのquantityカラムを数量と表示
FROM
    orders AS o -- ordersテーブルをoとする
INNER JOIN
    products AS p ON o.product_id = p.product_id -- ordersテーブルとproductsテーブルのproduct_idのデータ結合
WHERE
    o.customer_id = 1; -- ordersテーブルのcustomer_idが1の部分
```

(参考図)
<img width="1212" height="1380" alt="Image" src="https://github.com/user-attachments/assets/aaf61b6f-808e-4ff7-b338-ad2113a969de" />

- ノートPCを購入した顧客の一覧を取得（カラムは顧客名で表示）

・'ノートPC'名前からデータ取得する場合(ordersテーブルとproductsテーブルのデータ結合が必要)
```
SELECT   
    c.name AS 顧客名 -- customersテーブルのnameカラムを顧客名と表示
FROM
    orders AS o -- ordersテーブルをoとする
INNER JOIN
    customers AS c ON o.customer_id = c.customer_id -- ordersテーブルとcustomersテーブルのcustomer_idのデータ結合
INNER JOIN
    products AS p ON o.product_id = p.product_id -- ordersテーブルとproductsテーブルのproduct_idのデータ結合
WHERE
    p.product_name = 'ノートPC'; -- customersテーブルに'田中太郎'を含む項目の部分
```

・'ノートPC'のprodict_id(=1)がわかっているときにデータ取得する場合(ordersテーブルとproductsテーブルのデータ結合は不要)
```
SELECT   
    c.name AS 顧客名 -- customersテーブルのnameカラムを顧客名と表示
FROM
    orders AS o -- ordersテーブルをoとする
INNER JOIN
    customers AS c ON o.customer_id = c.customer_id -- ordersテーブルとcustomersテーブルのcustomer_idのデータ結合
WHERE
    o.product_id = 1; -- ordersテーブルのproduct_idが1の部分
```

(参考図)
<img width="1212" height="1389" alt="Image" src="https://github.com/user-attachments/assets/7c846e44-64a7-4939-b710-0bdce985718f" />


- 注文されていない商品を取得（LEFT JOINを使用）

```
SELECT
    p.product_name AS 商品名 -- product_nameを商品名と表示
FROM
    products AS p  -- ordersテーブルをoとする
LEFT JOIN
    orders AS o ON o.product_id = p.product_id -- ordersテーブルとproductsテーブルのproduct_idのデータ結合（NULL値も結合）
WHERE 
    o.product_id IS NULL; -- ordersテーブルでproduct_idが一度も出現していない部分
```

(参考図)
<img width="1212" height="1317" alt="Image" src="https://github.com/user-attachments/assets/c7428834-2601-43bb-a9e5-9f8c1d180d00" />
