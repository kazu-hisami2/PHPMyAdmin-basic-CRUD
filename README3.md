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
SELECT SUM(price) AS 売上合計金額 FROM sales;
```
(結果)
<img width="1212" height="1050" alt="Image" src="https://github.com/user-attachments/assets/a4175bb2-9928-4d0c-b55e-ce119b8890a4" />

- カテゴリ別の売上合計を計算（売上が高い順に表示）
- 最も売上が高い商品を取得（商品ごとの売上を集計）
- 売上が10万円以上の取引を取得
- 12月3日以降の売上を日付順に取得
- 平均以上の売上がある取引を取得（サブクエリを使用）
- 各カテゴリの商品数を集計（重複を除いた商品種類数と総数量）
