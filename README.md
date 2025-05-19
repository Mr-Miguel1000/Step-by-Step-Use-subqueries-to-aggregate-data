# Step-by-Step-Use-subqueries-to-aggregate-data
Objective: aggregate data into a table containing each warehouse's ID, state and alias, number of orders, grand total of orders for all warehouses combined, and a column of warehouse by percentage of grand total orders fulfilled: 0–20%, 21-60%, or > 60%. Last column MODIFIED: to exact percentage 
---Original---
SELECT
  Warehouse.warehouse_id,
  CONCAT(Warehouse.state, ': ', Warehouse.warehouse_alias) AS warehouse_name,
  COUNT(Orders.order_id) AS number_of_orders,
  (SELECT COUNT(*) FROM your-project.warehouse_orders.orders AS Orders) AS total_orders,
  CASE
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM your-project.warehouse_orders.orders AS Orders) <= 0.20
    THEN 'Fulfilled 0-20% of Orders'
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM your-project.warehouse_orders.orders AS Orders) > 0.20
    AND COUNT(Orders.order_id)/(SELECT COUNT(*) FROM your-project.warehouse_orders.orders AS Orders) <= 0.60
    THEN 'Fulfilled 21-60% of Orders'
    ELSE 'Fulfilled more than 60% of Orders'
  END AS fulfillment_summary
FROM your-project.warehouse_orders.warehouse AS Warehouse
LEFT JOIN your-project.warehouse_orders.orders AS Orders
ON Orders.warehouse_id = Warehouse.warehouse_id
GROUP BY
  Warehouse.warehouse_id,
  warehouse_name
HAVING
  COUNT(Orders.order_id) > 0
  
---Revision_1(5th column is now Rounded percentage column (two decimal places) 
SELECT
  Warehouse.warehouse_id,
  CONCAT(Warehouse.state, ': ', Warehouse.warehouse_alias) AS warehouse_name,
  COUNT(Orders.order_id) AS number_of_orders,
  (SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) AS total_orders,
 --CASE ~ Rounded percentage column (two decimal places) 
  CASE
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) <= 0.05
      THEN ROUND(100 * COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders),2)
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) > 0.05
      AND COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) <= 0.10
      THEN ROUND(100 * COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders), 2)
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) > 0.10
      AND COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) <= 0.20
      THEN ROUND(100 * COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders),2)
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) > 0.20
      AND COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) <= 0.30
      THEN ROUND(100 * COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders),2)
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) > 0.30
      AND COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) <= 0.60
      THEN ROUND(100 * COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders),2)
    ELSE NULL
  END AS order_percentage,
FROM `your-project.warehouse_orders.orders.warehouse` AS Warehouse
LEFT JOIN `your-project.warehouse_orders.orders` AS Orders
ON Orders.warehouse_id = Warehouse.warehouse_id
GROUP BY
  Warehouse.warehouse_id,
  warehouse_name
HAVING
  COUNT(Orders.order_id) > 0

---Revision_2
  --- (added 6th column - Re-added column with grand total of orders fulfilled. Options: 0–20%, 21–60%, or > 60%.)
  --- (added ORDER BY order_percentage DESC)
SELECT
  Warehouse.warehouse_id,
  CONCAT(Warehouse.state, ': ', Warehouse.warehouse_alias) AS warehouse_name,
  COUNT(Orders.order_id) AS number_of_orders,
  (SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) AS total_orders,
 --CASE ~ Rounded percentage column (two decimal places) 
  CASE
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) <= 0.05
      THEN ROUND(100 * COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders),2)
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) > 0.05
      AND COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) <= 0.10
      THEN ROUND(100 * COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders), 2)
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) > 0.10
      AND COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) <= 0.20
      THEN ROUND(100 * COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders),2)
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) > 0.20
      AND COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) <= 0.30
      THEN ROUND(100 * COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders),2)
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) > 0.30
      AND COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) <= 0.60
      THEN ROUND(100 * COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders),2)
    ELSE NULL
  END AS order_percentage,

  -- Fulfillment summary column ("Fulfilled at 0–20%, 21–60%, or 60% and over")
  CASE
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) <= 0.20
    THEN 'Fulfilled 0-20% of Orders'
    WHEN COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) > 0.20
    AND COUNT(Orders.order_id)/(SELECT COUNT(*) FROM `your-project.warehouse_orders.orders` AS Orders) <= 0.60
    THEN 'Fulfilled 21-60% of Orders'
    ELSE 'Fulfilled more than 60% of Orders'
  END AS fulfillment_summary
FROM `your-project.warehouse_orders.orders.warehouse` AS Warehouse
LEFT JOIN `your-project.warehouse_orders.orders` AS Orders
ON Orders.warehouse_id = Warehouse.warehouse_id
GROUP BY
  Warehouse.warehouse_id,
  warehouse_name
HAVING
  COUNT(Orders.order_id) > 0
ORDER BY order_percentage DESC
