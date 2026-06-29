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

   

# Database Implementation (SQL)
### DDL — Table Creation

CREATE TABLE CUSTOMER(
CUSTOMER_ID INT NOT NULL,
FIRST_NAME VARCHAR(255) NOT NULL,
LAST_NAME VARCHAR(255) NOT NULL,
ADDRESS VARCHAR(255) NOT NULL,
USER_PASSWORD VARCHAR(255) NOT NULL,
USERNAME VARCHAR(30) NOT NULL,
ZIP CHAR(6) NOT NULL,
CITY VARCHAR(50) NOT NULL,
STATE VARCHAR(50) NOT NULL,
PRIMARY KEY (CUSTOMER_ID),
UNIQUE (CUSTOMER_ID),
UNIQUE (USERNAME)
);
CREATE TABLE WEBSITE(
DOMAIN_NAME VARCHAR(255) NOT NULL,
CUSTOMER_ID INT NOT NULL,
PRIMARY KEY (DOMAIN_NAME),
FOREIGN KEY (CUSTOMER_ID) REFERENCES CUSTOMER(CUSTOMER_ID),
UNIQUE (DOMAIN_NAME)
);
CREATE TABLE ORDERS(
ORDER_ID INT NOT NULL,
ORDER_DATE DATE DEFAULT SYSDATE NOT NULL,
TOTAL_PRICE FLOAT NOT NULL,
CUSTOMER_ID INT NOT NULL,
PRIMARY KEY (ORDER_ID),
FOREIGN KEY (CUSTOMER_ID) REFERENCES CUSTOMER(CUSTOMER_ID)
);
CREATE TABLE PRODUCT(
PRODUCT_ID INT NOT NULL,
PRODUCT_NAME VARCHAR(255) NOT NULL,
PRODUCT_CATEGORY VARCHAR(255) NOT NULL,
PRODUCT_PRICE FLOAT NOT NULL CHECK (PRODUCT_PRICE > 0),
PRODUCT_AVAILABILITY INT NOT NULL,
ORDER_ID INT NOT NULL,
PRIMARY KEY (PRODUCT_ID),
FOREIGN KEY (ORDER_ID) REFERENCES ORDERS(ORDER_ID)
);
CREATE TABLE TRACKING_INFO(
TRACKING_ID INT NOT NULL,
COURIER VARCHAR(255) NOT NULL,
ORDER_ID INT NOT NULL,
PRIMARY KEY (TRACKING_ID),
FOREIGN KEY (ORDER_ID) REFERENCES ORDERS(ORDER_ID)
);
CREATE OR REPLACE TRIGGER prevent_duplicate_tracking
BEFORE INSERT ON TRACKING_INFO
FOR EACH ROW
DECLARE
existing_count INT;
BEGIN
SELECT COUNT(*) INTO existing_count
FROM TRACKING_INFO
WHERE ORDER_ID = :NEW.ORDER_ID;
IF existing_count > 0 THEN
RAISE_APPLICATION_ERROR(-20001, 'Each order can only have one tracking information');
END IF;
END;
/
CREATE TABLE PAYMENT_DETAILS(
PAYMENT_ID INT NOT NULL,
PAYMENT_DATE DATE NOT NULL,
PAYMENT_TYPE VARCHAR(255) NOT NULL,
CASH VARCHAR(10) DEFAULT 'YES' NOT NULL,
CARD INT NOT NULL,
CUSTOMER_ID INT NOT NULL,
PRIMARY KEY (PAYMENT_ID),
FOREIGN KEY (CUSTOMER_ID) REFERENCES CUSTOMER(CUSTOMER_ID)
);
CREATE TABLE CART(
CART_ID INT NOT NULL,
QUANTITY INT NOT NULL CHECK (QUANTITY > 0),
CUSTOMER_ID INT NOT NULL,
PRODUCT_ID INT NOT NULL,
PRIMARY KEY (CART_ID),
FOREIGN KEY (CUSTOMER_ID) REFERENCES CUSTOMER(CUSTOMER_ID),
FOREIGN KEY (PRODUCT_ID) REFERENCES PRODUCT(PRODUCT_ID)
);

### DML — Sample Data
-- Customers
INSERT INTO CUSTOMER VALUES (202000846, 'Haifa', 'Almutairi', 'Abdullah St', 'haifa00', 'haif
INSERT INTO CUSTOMER VALUES (202301026, 'Najla', 'Algarni', 'Khaled Road', 'najla321', 'najla
-- Orders
INSERT INTO ORDERS VALUES (101, TO_DATE('2025-01-10','YYYY-MM-DD'), 299.99, 202301026);
INSERT INTO ORDERS VALUES (102, TO_DATE('2025-01-12','YYYY-MM-DD'), 149.50, 202000846);
-- Payments
INSERT INTO PAYMENT_DETAILS VALUES (9002, TO_DATE('2025-01-12','YYYY-MM-DD'), 'Cash', 'Yes',
INSERT INTO PAYMENT_DETAILS VALUES (9001, TO_DATE('2025-01-10','YYYY-MM-DD'), 'Card', 'No', 1
-- Products
INSERT INTO PRODUCT VALUES (1001, 'Wallets', 'Accessories', 29.99, 50, 101);
INSERT INTO PRODUCT VALUES (1002, 'Headbands', 'Accessories', 19.99, 100, 101);
INSERT INTO PRODUCT VALUES (1003, 'Dresses', 'Clothing', 99.50, 30, 102);
-- Tracking
INSERT INTO TRACKING_INFO VALUES (501, 'Naqel', 101);
INSERT INTO TRACKING_INFO VALUES (502, 'SBL', 102);
-- Website
INSERT INTO WEBSITE (DOMAIN_NAME, CUSTOMER_ID) VALUES ('HN-CLOTHS', 202000846);
-- Cart
INSERT INTO CART VALUES (7002, 1, 202000846, 1002);
INSERT INTO CART VALUES (7001, 2, 202301026, 1001);
INSERT INTO CART VALUES (7003, 3, 202301026, 1003);
