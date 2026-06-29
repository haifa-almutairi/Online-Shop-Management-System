# Online-Shop-Management-System
A relational database design for an e-commerce platform, built as a database project. 
The system manages customers, products, orders, payments, shipment tracking, and shopping carts for an online store.

## Overview
Online shopping requires a well-structured database to keep track of products, customers,
orders, and payments accurately and efficiently. This project models that system end-to-
end: from entity design and constraints, to an ER diagram, relational schema, and working
SQL implementation on Oracle.

The system is designed to:
- Manage customer accounts and information reliably
- Process orders accurately
- Handle secure payment records
- Track inventory and product data
- Support shipment tracking per order
- Maintain a shopping cart per customer


## Goals and Objectives
Goals
- Efficient management of customer information and accounts
- Accurate and reliable order processing
- Secure and smooth payment processing
- Proper inventory and product data management
- A user-friendly interface for both administrators and customers
- Well-organized and easily retrievable data
- Consistent and high-performing system operation

  
Objectives
- Maintain accuracy and reliability of stored customer and order data
- Reduce manual errors in order and payment processes
- Ensure secure online payment transactions
- Improve data retrieval speed for searches and reports
- Keep data relationships consistent through key constraints


## Key Constraints
- All primary keys must be unique

- Username (Customer) must be unique
  
- Product_Price must be greater than 0
  
- Quantity (Cart) must be greater than 0
  
- Each order can have only one tracking record (enforced via trigger)

- Order_Date defaults to the current date if not provided

- Payment_Type defaults to “CASH” if not specified
  
- All foreign keys must reference an existing record in their parent table


## Relationships
1. Website → Customer (1:N)
2. Customer → Order (1:N)
3. Customer → Payment Details (1:1)
4. Order → Tracking Information (1:1)
5. Product → Order (1:N)
6. Customer → Cart (1:1)
7. Cart → Product (N:1)


## Scenario
1. A customer registers and logs into the website.
2. The customer browses available products.
3. They add selected items to their cart.
4. The customer places an order, which stores product details and pricing.
5. Payment information is recorded in the Payment Details table.
6. The system assigns tracking information so the customer can monitor the delivery.

