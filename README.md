# phpMyAdminによるデータベース操作の覚書1

## 概要

- MySQLの基本操作CRUD（作成、データ取得、更新、削除）をおさえるためのハンズオン演習です。  
  taskをまとめた表をデータベースに作成し、その変更や削除について一連の流れをまとめました。

### データベースおよびテーブルの作成

- task_management データベースの作成

```
CREATE DATABASE task_management;
```

- tasks テーブルの作成

```
CREATE TABLE tasks (
task_id INT AUTO_INCREMENT,
title VARCHAR(255) NOT NULL,
description TEXT NULL,
status ENUM('未着手', '進行中', '完了') DEFAULT '未着手',
priority INT DEFAULT 3,
due_date DATE NULL,
created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
PRIMARY KEY (task_id)
);
```

(ここまでの参考図)  
<img width="1686" height="2112" alt="Image" src="https://github.com/user-attachments/assets/1aa9fd76-0e58-47b4-bbb1-c5b59792f9d6" />

### データの追加

- 5つのタスクをカラムに追加（DATE型は文字列扱いなので''(シングルクォーテーション)でくくろう！）

```
INSERT INTO users (title, status, priority, due_date)
VALUES
('データベース設計', '完了', 1, '2024-12-10'),
('API開発', '進行中', 1, '2024-12-20'),
('テスト作成', '未着手', 2, '2024-12-25'),
('ドキュメント作成', '未着手', 3, '2024-12-30'),
('デプロイ準備', '未着手', 2, '2024-01-05');
```

(ここまでの参考図)  
<img width="1599" height="1451" alt="Image" src="https://github.com/user-attachments/assets/e23d5ead-625c-4756-bec4-1d8552d66d73" />

### データの取得

- すべてのタスクを取得

```
SELECT * FROM tasks;
```

- ステータスが「未着手」のタスクのみ取得

```
SELECT * FROM tasks
WHERE status = '未着手';
```

(参考図)  
<img width="1599" height="1338" alt="Image" src="https://github.com/user-attachments/assets/163a9b28-c668-4202-a288-0246e2ab186a" />

- 優先度が1（高）のタスクのみ取得

```
SELECT * FROM tasks
WHERE priority = 1;
```

(参考図)  
<img width="1598" height="1217" alt="Image" src="https://github.com/user-attachments/assets/a2f8cff2-3e10-4cd4-adf6-f5b40cfee6c6" />

- タスクを優先度の高い順に並び替えて取得(ASCは昇順、DESCは降順)

```
SELECT * FROM tasks
ORDER BY priority ASC;
```

(参考図)  
<img width="1649" height="1551" alt="Image" src="https://github.com/user-attachments/assets/1925722c-c9d4-4bf7-9672-43628ef6c427" />

### データの更新

- 「API開発」のステータスを「完了」に変更

```
UPDATE tasks
SET status = '完了'
WHERE task_id = 2;
```

(参考図)  
<img width="1599" height="1451" alt="Image" src="https://github.com/user-attachments/assets/bdcefa62-1c0f-47e7-84d3-0b42cfb16086" />

- 「テスト作成」のステータスを「進行中」、優先度を1に変更

```
UPDATE tasks
SET
    status = '進行中',
    priority = 1
WHERE
    task_id = 3;
```

(参考図)  
<img width="1599" height="1451" alt="Image" src="https://github.com/user-attachments/assets/b813d8e3-e342-4789-8367-97348c16917a" />

### データの削除

- 「ドキュメント作成」タスクの削除

```
DELETE FROM tasks
WHERE task_id = 4;
```

(参考図)  
<img width="1598" height="1328" alt="Image" src="https://github.com/user-attachments/assets/6922e088-b086-4f0b-b86f-3e5d5f63e907" />
