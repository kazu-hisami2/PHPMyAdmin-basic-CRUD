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
```
SELECT
    s.product_name AS 商品名,
    (s.price * s.quantity) AS 売上,
    s.sale_date AS 取引日
FROM sales AS s
WHERE (s.price * s.quantity) >= 100000
ORDER BY 売上 DESC;
```
(結果)
<img width="1581" height="1142" alt="Image" src="https://github.com/user-attachments/assets/95698b97-c312-4188-8a0b-e131ae5ab8ef" />

- 12月3日以降の売上を日付順に取得
```
SELECT
	s.sale_date AS 取引日,
    SUM(s.price*s.quantity) AS 合計売上金額
FROM
	sales AS s
WHERE
	s.sale_date >= '2024-12-03' -- 日にちが2024-12-03以降のものを返す(GROUP BYより前の処理)
GROUP BY
	s.sale_date -- 取引日ごとにグループ分け(SELECT前の処理)
ORDER BY
	取引日 ASC; -- 取引日が古いもの順にソート
```
(結果)
<img width="1212" height="1419" alt="Image" src="https://github.com/user-attachments/assets/67f4674a-fc35-46b4-84a4-200a49189c4d" />

・参考(12月2日から12月5日までの売上を日付順に取得)
```
SELECT
	s.sale_date AS 取引日,
    SUM(s.price*s.quantity) AS 合計売上金額
FROM
	sales AS s
WHERE
	s.sale_date BETWEEN '2024-12-02' AND '2024-12-05' -- 日にちが2024-12-02から2024-12-05のものを返す(GROUP BYより前の処理)
GROUP BY
	s.sale_date -- 取引日ごとにグループ分け(SELECT前の処理)
ORDER BY
	取引日 ASC; -- 取引日が古いもの順にソート
```
(結果)
<img width="1212" height="1419" alt="Image" src="https://github.com/user-attachments/assets/7288e9ed-8586-4961-ab52-34655beec308" />

- 平均以上の売上がある取引を取得（サブクエリを使用）
```
WITH daily_sales AS (
    SELECT sale_date, SUM(price * quantity) AS total_sales
    FROM sales
    GROUP BY sale_date
)
-- 「日ごとの売上表」を1回だけ定義して「daily_sales」と命名(WITHを用いることでクエリ内でサブクエリのdaily_salesテーブルを使用可能)

SELECT
	d.sale_date AS 取引日,
    d.total_sales AS 合計売上金額
FROM
	daily_sales AS d
WHERE
	d.total_sales >= (SELECT AVG(daily_sales.total_sales) FROM daily_sales); -- サブクエリ内なので"d"のエイリアスは使えないことに注意！
```
(結果)
<img width="1212" height="1340" alt="Image" src="https://github.com/user-attachments/assets/ae38673d-a9bf-41e8-a3d1-69c56d1891f0" />

- 各カテゴリの商品数を集計（重複を除いた商品種類数と総数量）
```
SELECT
	s.category AS カテゴリー,
    COUNT(DISTINCT s.product_name) AS 商品種類数, -- 重複を除いた「商品の種類数」をカウント
    SUM(s.quantity) AS 総数量 -- 売れた「総数量」を合計
FROM
	sales AS s
GROUP BY
	s.category
```
(結果)
<img width="1212" height="1142" alt="Image" src="https://github.com/user-attachments/assets/b43a5ab4-2b08-4086-90f5-842c108d8c44" />
