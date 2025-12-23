# Exercise: MySQL - E-Commerce Database

## Objective
Design and implement a relational database schema for an e-commerce platform.

## Requirements

### Required Files
| File | Description |
|------|-------------|
| `schema.sql` | Table definitions (CREATE statements) |
| `seed_data.sql` | Sample data (INSERT statements) |
| `queries.sql` | Required queries |

### Database Schema

Design tables for:
1. **customers** - Customer information
2. **products** - Product catalog
3. **categories** - Product categories
4. **orders** - Customer orders
5. **order_items** - Items in each order

### schema.sql Requirements
```sql
-- Required tables with minimum columns:

-- customers: id, name, email, phone, address, created_at
-- products: id, name, description, price, stock_quantity, category_id
-- categories: id, name, description
-- orders: id, customer_id, order_date, total_amount, status
-- order_items: id, order_id, product_id, quantity, unit_price
```

### Technical Requirements
- [ ] Use appropriate data types
- [ ] Define PRIMARY KEY for all tables
- [ ] Define FOREIGN KEY relationships
- [ ] Add NOT NULL constraints where appropriate
- [ ] Add DEFAULT values where sensible
- [ ] Create at least one INDEX

### queries.sql - Required Queries
Write SQL queries for:

1. List all products with their category names
2. Find customers who placed orders in the last 30 days
3. Calculate total revenue per category
4. Find top 5 best-selling products
5. List orders with total items and amount per order
6. Find products that are low in stock (< 10)
7. Calculate average order value per customer
8. Find categories with no products

## Sample Schema Structure
```sql
CREATE TABLE categories (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE products (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(200) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL,
    stock_quantity INT DEFAULT 0,
    category_id INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (category_id) REFERENCES categories(id)
);
-- Continue for other tables...
```

## Evaluation Criteria
| Criteria | Points |
|----------|--------|
| Correct table structure | 20 |
| Proper relationships (FK) | 20 |
| Constraints & indexes | 15 |
| Seed data quality | 15 |
| Query correctness | 30 |
| **Total** | **100** |

## Submission Checklist
- [ ] All three `.sql` files present
- [ ] Placed in `09-mysql/` folder
- [ ] Scripts run without errors
- [ ] Foreign keys properly defined
- [ ] Committed and pushed to repository
