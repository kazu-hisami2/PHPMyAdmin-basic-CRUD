# phpMyAdminによるデータベース操作の覚書3

## 概要

- MySQLの集計関数やサブクエリをおさえるためのハンズオン演習です。  
  サンプルの売上データを管理するデータベースとテーブルを用いて様々な角度から売上を分析するクエリを作成します。

### データベースの作成
```
CREATE DATABASE sales_analysis;
```

### テーブルの作成、サンプルデータの挿入
- salesテーブル（サンプルデータの挿入まで）
```
CREATE TABLE sales (
    sale_id INT PRIMARY KEY AUTO_INCREMENT,
    product_name VARCHAR(100) NOT NULL,
    category VARCHAR(50) NOT NULL,
    price INT NOT NULL,
    quantity INT NOT NULL,
    sale_date DATE NOT NULL
);

INSERT INTO sales (product_name, category, price, quantity, sale_date) VALUES
('ノートPC', '電子機器', 120000, 2, '2024-12-01'),
('マウス', '電子機器', 2000, 10, '2024-12-01'),
('キーボード', '電子機器', 5000, 5, '2024-12-02'),
('モニター', '電子機器', 30000, 3, '2024-12-02'),
('デスク', '家具', 25000, 2, '2024-12-03'),
('チェア', '家具', 15000, 4, '2024-12-03'),
('本棚', '家具', 10000, 3, '2024-12-04'),
('ノートPC', '電子機器', 120000, 1, '2024-12-05'),
('マウス', '電子機器', 2000, 15, '2024-12-05'),
('デスク', '家具', 25000, 1, '2024-12-06');

```

(ここまでの経過)
<img width="1581" height="1373" alt="Image" src="https://github.com/user-attachments/assets/64997f36-cf9d-49e8-a9ee-8bb31ab08db7" />

### 作成するクエリについて 
- 売上合計金額を計算（price × quantity の合計）
```
SELECT SUM(s.price*s.quantity) AS 売上合計金額 FROM sales AS s;
```
(結果)
<img width="1212" height="1050" alt="Image" src="https://github.com/user-attachments/assets/32c28aaf-170a-4fe6-a311-4ac978bd4466" />

- カテゴリ別の売上合計を計算（売上が高い順に表示）
```
SELECT
	s.category AS カテゴリー,
    SUM(s.price*s.quantity) AS 売上
FROM
	sales AS s
GROUP BY
	s.category -- カテゴリーごとにグループ分け(SELECT前の処理)
ORDER BY
	売上 DESC; -- カテゴリ別の売上が高い順に表示
```
(結果)
<img width="1212" height="1322" alt="Image" src="https://github.com/user-attachments/assets/8c2c2cd4-a81e-4027-bf03-b43f3d31a8cf" />

- 最も売上が高い商品を取得（商品ごとの売上を集計）
・売上が最高の商品の名前と売上を表示
```
SELECT
	s.product_name AS 商品名,
    SUM(s.price*s.quantity) AS 売上
FROM
	sales AS s
GROUP BY
	s.product_name; -- 商品ごとにグループ分け(SELECT前の処理)
ORDER BY
	売上 DESC LIMIT 1; -- 売上1位の商品のみ表示
```
(結果)
<img width="1182" height="1122" alt="Image" src="https://github.com/user-attachments/assets/1c22e977-8183-45fa-b60d-f504f0ed2fb3" />

・参考（商品ごとの売り上げランキング）
```
SELECT
	s.product_name AS 商品名,
    SUM(s.price*s.quantity) AS 売上
FROM
	sales AS s
GROUP BY
	s.product_name -- 商品ごとにグループ分け(SELECT前の処理)
ORDER BY
	売上 DESC; -- 商品別の売上が高い順に表示
```
(結果)
<img width="1212" height="1488" alt="Image" src="https://github.com/user-attachments/assets/39ed94c1-3fc0-4371-82f4-ebf6076284a3" />

- 売上が10万円以上の取引を取得
- 12月3日以降の売上を日付順に取得
- 平均以上の売上がある取引を取得（サブクエリを使用）
- 各カテゴリの商品数を集計（重複を除いた商品種類数と総数量）
