# Database Schema (Part 2)

## Table Definitions

### Users Table

**Table Name:** `users`

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique identifier for user |
| email | VARCHAR(255) | UNIQUE, NOT NULL | User's email address |
| password_hash | VARCHAR(255) | | Encrypted password (null for social-only) |
| email_verified | BOOLEAN | DEFAULT false | Whether email has been verified |
| display_name | VARCHAR(100) | | User's display name |
| phone_number | VARCHAR(20) | | Optional phone number |
| profile_image_url | VARCHAR(255) | | URL to profile image |
| points_balance | INTEGER | DEFAULT 0, NOT NULL | Current points balance |
| lifetime_points | INTEGER | DEFAULT 0, NOT NULL | Total points earned |
| created_at | TIMESTAMP | DEFAULT NOW(), NOT NULL | Account creation timestamp |
| updated_at | TIMESTAMP | DEFAULT NOW(), NOT NULL | Last update timestamp |
| last_login | TIMESTAMP | | Last login timestamp |
| status | ENUM | DEFAULT 'active', NOT NULL | User status (active, suspended, deleted) |

**Indexes:**
- Primary Key: `id`
- Unique Index: `email`
- Index: `status`

### Receipts Table

**Table Name:** `receipts`

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique identifier for receipt |
| user_id | UUID | FOREIGN KEY, NOT NULL | Reference to users table |
| created_at | TIMESTAMP | DEFAULT NOW(), NOT NULL | When receipt was scanned |
| store_name | VARCHAR(100) | NOT NULL | Name of store from receipt |
| receipt_date | DATE | NOT NULL | Date on receipt |
| total_amount | DECIMAL(10,2) | NOT NULL | Total amount on receipt |
| item_count | INTEGER | | Number of items on receipt |
| items | JSONB | | Array of item objects with name, price, etc. |
| image_url | VARCHAR(255) | | URL to stored receipt image |
| points_awarded | INTEGER | DEFAULT 0, NOT NULL | Total points awarded for receipt |
| status | ENUM | DEFAULT 'processed', NOT NULL | Status (processed, pending_review, rejected) |
| ocr_confidence | FLOAT | | Confidence score from OCR processing |
| is_manual_entry | BOOLEAN | DEFAULT false, NOT NULL | Whether receipt was manually entered |

**Indexes:**
- Primary Key: `id`
- Foreign Key: `user_id` references `users(id)`
- Index: `receipt_date`
- Index: `store_name`
- Index: `created_at`
- Compound Index: `(user_id, store_name, receipt_date, total_amount)` for duplicate detection

### Deals Table

**Table Name:** `deals`

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique identifier for deal |
| external_id | VARCHAR(100) | | Original ID from affiliate platform |
| affiliate_source | ENUM | NOT NULL | Source platform (ibotta, rakuten, etc.) |
| product_name | VARCHAR(200) | NOT NULL | Product name |
| description | TEXT | | Deal description |
| brand | VARCHAR(100) | | Product brand |
| category | VARCHAR(50) | | Product category |
| reward_type | ENUM | NOT NULL | Type (points, cashback, percentage) |
| reward_value | DECIMAL(10,2) | NOT NULL | Value in original format |
| points_value | INTEGER | NOT NULL | Converted to app points value |
| image_url | VARCHAR(255) | | URL to product image |
| terms | TEXT | | Terms and conditions |
| start_date | TIMESTAMP | NOT NULL | When deal becomes active |
| end_date | TIMESTAMP | NOT NULL | When deal expires |
| stores | VARCHAR[] | | Array of eligible store names |
| required_quantity | INTEGER | DEFAULT 1, NOT NULL | Quantity required for eligibility |
| limit_per_receipt | INTEGER | | Max claims per receipt (null = unlimited) |
| limit_per_user | INTEGER | | Max claims per user (null = unlimited) |
| barcode | VARCHAR(50) | | UPC/EAN when available |
| created_at | TIMESTAMP | DEFAULT NOW(), NOT NULL | When deal was added to system |
| updated_at | TIMESTAMP | DEFAULT NOW(), NOT NULL | When deal was last updated |
| is_featured | BOOLEAN | DEFAULT false, NOT NULL | Whether deal is featured |
| popularity_score | FLOAT | DEFAULT 0, NOT NULL | Calculated popularity for sorting |

**Indexes:**
- Primary Key: `id`
- Index: `affiliate_source`
- Index: `product_name`
- Index: `brand`
- Index: `category`
- Index: `end_date`
- GIN Index on `stores` array
- GIN Index on `items->>'name'` for JSONB text search
