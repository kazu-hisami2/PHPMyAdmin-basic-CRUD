# phpMyAdminの覚書2

## 


'''
SELECT 
    c.name AS 顧客名,
    p.name AS 商品名,
    o.quantity AS 数量,
    o.order_date AS 注文日
FROM 
    `order` AS o
INNER JOIN 
    `customer` AS c ON o.customer_id = c.id
INNER JOIN 
    `product` AS p ON o.product_id = p.id;
'''
