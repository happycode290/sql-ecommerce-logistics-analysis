<p align="center">
  <img src="https://img.shields.io/badge/SQL-PostgreSQL-blue?style=for-the-badge&logo=postgresql&logoColor=white" />
  <img src="https://img.shields.io/badge/Data%20Analysis-Logistics-orange?style=for-the-badge&logo=data" />
  <img src="https://img.shields.io/badge/Workflow-Modeling-success?style=for-the-badge&logo=gitbook" />
  <img src="https://img.shields.io/badge/Domain-E--commerce-red?style=for-the-badge&logo=shopify&logoColor=white" />
  <img src="https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge" />
</p>


# SQL E-Commerce Logistics Analysis

## 📌 Project Overview
This project explores how SQL supports critical e-commerce logistics workflows such as inventory checks, purchase orders, batching, warehouse picking/packing, routing, and last-mile delivery. I worked with a realistic relational database schema and executed SQL queries to analyze customer behavior, warehouse efficiency, batch performance, and delivery route metrics.  
The project demonstrates understanding of data modeling, SQL joins, aggregations, workflow mapping, and operational analytics.


## 📦 Inventory Workflow Analysis
1. Inventory Check and Stock Evaluation  
2. Purchase Order (PO) Creation  
3. PO Transmission to Vendor  
4. Shipment and Invoice Generation by Vendor  
5. Goods Receipt and Inspection at Warehouse  
6. Goods Receipt Note (GRN) Generation  
7. Inventory Update in IMS  
8. Inventory Adjustments from Other Events


## 🛒 Order Workflow Analysis
1. Customer Order Placement  
2. Inventory Allocation for Orders  
3. Batch Generation (Warehouse Processing)  
4. Picking Process  
5. Packing Process  
6. Dispatch to Delivery Hub (Middle Mile)  
7. Receiving at Delivery Hub  
8. Last-Mile Delivery (Out for Delivery - OFD)  
9. Delivery Execution & Return to Origin (RTO)  
10. Reconciliation and Route Closure  



## 🗄️ Database Schema — Tables Used
- `tbl_admin`  
- `customers`  
- `orders`  
- `wh_batch_shifts`  
- `wh_packing_tables`  
- `batch_summary`  
- `db_routes`  
- `db_route_mapping`  

---

## 🧠 SQL Analysis Queries

### 1 Total Orders Placed per Customer
```
SELECT c.id AS customer_id,
       c.name AS customer_name,
       COUNT(o.order_id) AS total_orders
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
GROUP BY c.id, c.name
ORDER BY total_orders DESC
LIMIT 10;
```

### 2 Batches with the Most Unpacked Items

```
SELECT batch_id,
       line_items_count,
       packed_line_items_count,
       (line_items_count - packed_line_items_count) AS items_pending,
       is_closed,
       created_at
FROM batch_summary
WHERE is_closed = FALSE
ORDER BY items_pending DESC
LIMIT 10;
```

### 3️ Delivery Route Performance Matrix
```
SELECT r.route_id,
       COUNT(ro.order_id) AS total_orders,
       r.total_cash_collected,
       r.total_upi_collected,
       r.completed_at
FROM db_routes r
LEFT JOIN route_orders ro ON r.route_id = ro.route_id
GROUP BY r.route_id, r.total_cash_collected, r.total_upi_collected, r.completed_at
ORDER BY r.completed_at DESC
LIMIT 10;
```

### 4️ Total Deliveries by Each Delivery Boy
```
SELECT a.admin_name AS delivery_boy,
       COUNT(drm.route_id) AS total_deliveries
FROM db_route_mapping drm
JOIN tbl_admin a ON drm.delivery_boy_id = a.admin_id
GROUP BY a.admin_name
ORDER BY total_deliveries DESC;
```

### 5️ Average Orders per Customer
```
SELECT c.id AS customer_id,
       c.name AS customer_name,
       COUNT(o.order_id) AS total_orders,
       ROUND(COUNT(o.order_id) * 1.0 /
       (DATE_PART('day', MAX(o.order_date) - MIN(o.order_date)) + 1), 2)
       AS avg_orders_per_day
FROM customers c
JOIN orders o ON c.id = o.customer_id
GROUP BY c.id, c.name
ORDER BY avg_orders_per_day DESC
LIMIT 10;
```

### 6️ Inactive Delivery Boys (No Routes in Last 7 Days)
```
SELECT a.admin_id,
       a.admin_name
FROM tbl_admin a
LEFT JOIN db_route_mapping drm
ON a.admin_id = drm.delivery_boy_id
   AND drm.delivery_date >= CURRENT_DATE - INTERVAL '7 days'
WHERE drm.route_id IS NULL;
```

### 7 Average Delivery Completion Time
```
SELECT AVG(EXTRACT(EPOCH FROM (completed_at - started_at)) / 60)
       AS avg_delivery_minutes
FROM db_routes
WHERE completed_at IS NOT NULL
  AND started_at IS NOT NULL;
  ```

## 📊 Key Findings

- `SQL enables real-time logistics visibility`

- `Supports warehouse performance tracking`

- `Helps identify inactive delivery staff`

- `Reveals customer ordering trends`

- `Enables route-level payment insights`

-` Improves batching, packing, and delivery operations`


## 📎 Project File

The complete research report is available in this repository as a PDF.

## 👤 Author

### Avinash Kumar Kanojiya
Aspirant Data Analyst | SQL | Power BI | Python
