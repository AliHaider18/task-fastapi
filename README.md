# E-commerce Admin API

An API that powers a web admin dashboard for e-commerce managers, providing detailed insights into sales, revenue, and inventory status, as well as allowing new product registration. Built with Python and FastAPI.

## Features

- _Sales Analysis_

  - Retrieve, filter, and analyze sales data
  - Analyze revenue by different time periods (daily, weekly, monthly, yearly)
  - Compare revenue across different periods and categories
  - Filter sales data by date range, product, and category

- _Inventory Management_

  - View current inventory status and low stock alerts
  - Update inventory levels
  - Track inventory changes over time

- _Product Management_

  - Register and manage products
  - Categorize products

- _Analytics_
  - Revenue breakdowns by category and product
  - Performance comparisons across time periods
  - Identify high and low-performing products

## Database Schema

The database schema includes the following tables:

1. _Categories_

   - id (Primary Key): Unique identifier for each category
   - name (String, Unique): Category name, indexed for quick lookups
   - description (Text): Detailed category description
   - _Relationships_: One-to-many with Products

2. _Products_

   - id (Primary Key): Unique identifier for each product
   - sku (String, Unique): Stock keeping unit, indexed for quick lookups
   - name (String): Product name, indexed for search
   - description (Text): Detailed product description
   - price (Float): Current selling price
   - cost (Float): Product cost price for margin calculations
   - category_id (Foreign Key): References Categories table
   - image_url (String): URL to product image
   - created_at (Timestamp): Creation timestamp
   - updated_at (Timestamp): Last update timestamp
   - _Relationships_:
     - Many-to-one with Categories
     - One-to-one with Inventory
     - One-to-many with SaleItems

3. _Inventory_

   - id (Primary Key): Unique identifier for each inventory record
   - product_id (Foreign Key, Unique): References Products table
   - quantity (Integer): Current stock quantity
   - low_stock_threshold (Integer): Threshold for low stock alerts
   - last_restock_date (Timestamp): Date of last restock
   - created_at (Timestamp): Creation timestamp
   - updated_at (Timestamp): Last update timestamp
   - _Relationships_:
     - One-to-one with Products
     - One-to-many with InventoryChanges

4. _InventoryChanges_

   - id (Primary Key): Unique identifier for each change record
   - inventory_id (Foreign Key): References Inventory table
   - quantity_change (Integer): Change in quantity (positive for additions, negative for reductions)
   - reason (Text): Reason for the inventory change
   - created_at (Timestamp): When the change occurred
   - _Relationships_: Many-to-one with Inventory

5. _Sales_

   - id (Primary Key): Unique identifier for each sale
   - reference_number (String, Unique): Unique sale reference number
   - total_amount (Float): Total sale amount including tax
   - tax_amount (Float): Tax amount
   - discount_amount (Float): Total discount applied
   - payment_method (Enum): Payment method used
   - customer_name (String): Customer name
   - customer_email (String): Customer email
   - notes (Text): Additional sale notes
   - created_at (Timestamp): Sale timestamp, indexed for filtering
   - _Relationships_: One-to-many with SaleItems

6. _SaleItems_
   - id (Primary Key): Unique identifier for each sale item
   - sale_id (Foreign Key): References Sales table
   - product_id (Foreign Key): References Products table
   - quantity (Integer): Quantity sold
   - price (Float): Price at time of sale
   - discount (Float): Item-level discount
   - total (Float): Total for this item
   - _Relationships_:
     - Many-to-one with Sales
     - Many-to-one with Products

## Key Design Decisions

1. _Indexing Strategy_

   - Primary keys are automatically indexed
   - Foreign keys are indexed for join performance
   - Frequently queried fields (sku, name, created_at) are indexed
   - Unique constraints create automatic indexes

2. _Audit Trail_

   - Timestamps track creation and updates
   - InventoryChanges table maintains complete inventory history
   - Sales data is immutable once created

3. _Data Integrity_

   - Foreign key constraints ensure referential integrity
   - Unique constraints prevent duplicate records
   - Default values and not-null constraints maintain data quality

4. _Performance Considerations_
   - Denormalized price in SaleItems captures historical pricing
   - Separate InventoryChanges table allows efficient current quantity queries
   - Indexed timestamps optimize date-range queries

## Setup Instructions

### Prerequisites

- Python 3.8+
- PostgreSQL

### Installation

1. Clone the repository:
   bash
   git clone https://github.com/yourusername/ecommerce-admin-api.git
   cd ecommerce-admin-api

2. Create a virtual environment:
   bash
   python -m venv venv
   source venv/bin/activate # On Windows: venv\Scripts\activate

3. Install dependencies:
   bash
   pip install -r requirements.txt

4. Create a .env file with your database connection string:

   DATABASE_URL=postgresql://user:password@localhost:5432/ecommerce

   A database url with already seeded data is provided in the pdf document.

5. Run the application:
   bash
   npm run dev

6. Seed the database with demo data:
   bash
   npm run seed

## API Endpoints

Once the application is running, you can access the Swagger UI documentation at:

```
http://localhost:8000/docs
```

This provides a detailed, interactive documentation of all available endpoints.

### Key Endpoints

#### Categories

- `GET /api/categories` - Get all categories
- `POST /api/categories` - Create a new category
- `GET /api/categories/{category_id}` - Get a specific category
- `PATCH /api/categories/{category_id}` - Update a category
- `DELETE /api/categories/{category_id}` - Delete a category

#### Products

- `GET /api/products` - Get all products
- `POST /api/products` - Create a new product
- `GET /api/products/{product_id}` - Get a specific product
- `PATCH /api/products/{product_id}` - Update a product
- `DELETE /api/products/{product_id}` - Delete a product
- `GET /api/products/with-inventory` - Get products with inventory info

#### Inventory

- `GET /api/inventory` - Get all inventory
- `GET /api/inventory/low-stock` - Get low stock alerts
- `GET /api/inventory/{inventory_id}` - Get specific inventory
- `PATCH /api/inventory/{inventory_id}` - Update inventory
- `POST /api/inventory/changes` - Record inventory change
- `GET /api/inventory/changes/{inventory_id}` - Get inventory changes

#### Sales

- `GET /api/sales` - Get all sales
- `POST /api/sales` - Create a new sale
- `GET /api/sales/{sale_id}` - Get a specific sale
- `POST /api/sales/filter` - Filter sales

#### Analytics

- `GET /api/analytics/revenue` - Get revenue data
- `POST /api/analytics/compare-periods` - Compare revenue between periods
- `GET /api/analytics/category-revenue` - Get revenue by category
- `GET /api/analytics/product-revenue` - Get revenue by product
- `GET /api/analytics/low-performing-products` - Get low performing products