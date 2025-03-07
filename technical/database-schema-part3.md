# Database Schema (Part 3)

## Additional Table Definitions

### Stores Table

**Table Name:** `stores`

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique identifier for store |
| name | VARCHAR(100) | NOT NULL | Store name |
| chain | VARCHAR(100) | | Parent company or chain name |
| address | VARCHAR(255) | | Street address |
| city | VARCHAR(100) | | City |
| state | VARCHAR(50) | | State/province |
| postal_code | VARCHAR(20) | | ZIP/postal code |
| country | VARCHAR(50) | DEFAULT 'US', NOT NULL | Country code |
| latitude | FLOAT | NOT NULL | Geolocation latitude |
| longitude | FLOAT | NOT NULL | Geolocation longitude |
| geofence_radius | INTEGER | DEFAULT 50, NOT NULL | Radius in meters for geofence |
| supported | BOOLEAN | DEFAULT true, NOT NULL | Whether store is supported for receipt scanning |
| popularity_score | FLOAT | DEFAULT 0, NOT NULL | Calculated score for ranking |
| last_updated | TIMESTAMP | DEFAULT NOW(), NOT NULL | When store data was last updated |

**Indexes:**
- Primary Key: `id`
- Index: `name`
- Index: `chain`
- Index: `postal_code`
- Spatial Index: `(latitude, longitude)` for geospatial queries

### Store Visits Table

**Table Name:** `store_visits`

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique identifier for visit |
| user_id | UUID | FOREIGN KEY, NOT NULL | Reference to users table |
| store_id | UUID | FOREIGN KEY, NOT NULL | Reference to stores table |
| entered_at | TIMESTAMP | NOT NULL | When user entered store geofence |
| exited_at | TIMESTAMP | | When user exited store geofence |
| notification_sent | BOOLEAN | DEFAULT false, NOT NULL | Whether exit notification was sent |
| notification_time | TIMESTAMP | | When notification was sent |
| receipt_scanned | BOOLEAN | DEFAULT false, NOT NULL | Whether receipt was scanned for this visit |
| receipt_id | UUID | FOREIGN KEY | Reference to scanned receipt |
| scan_time | TIMESTAMP | | When receipt was scanned |

**Indexes:**
- Primary Key: `id`
- Foreign Key: `user_id` references `users(id)`
- Foreign Key: `store_id` references `stores(id)`
- Foreign Key: `receipt_id` references `receipts(id)`
- Index: `entered_at`
- Index: `exited_at`
- Compound Index: `(user_id, store_id, entered_at)` for quick user visit lookup

### Points Transactions Table

**Table Name:** `points_transactions`

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique identifier for transaction |
| user_id | UUID | FOREIGN KEY, NOT NULL | Reference to users table |
| transaction_type | ENUM | NOT NULL | Type (earn, redeem) |
| amount | INTEGER | NOT NULL | Points amount (positive for earn, negative for redeem) |
| source | ENUM | NOT NULL | Source (receipt, deal_match, referral, redemption) |
| source_id | UUID | | Reference to source object (receipt_id, deal_id, etc.) |
| description | VARCHAR(255) | NOT NULL | Human-readable description |
| balance_after | INTEGER | NOT NULL | Points balance after transaction |
| created_at | TIMESTAMP | DEFAULT NOW(), NOT NULL | When transaction occurred |
| status | ENUM | DEFAULT 'completed', NOT NULL | Status (pending, completed, reversed) |

**Indexes:**
- Primary Key: `id`
- Foreign Key: `user_id` references `users(id)`
- Index: `transaction_type`
- Index: `source`
- Index: `created_at`
- Index: `status`

### Rewards Table

**Table Name:** `rewards`

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique identifier for reward |
| title | VARCHAR(100) | NOT NULL | Reward title |
| description | TEXT | | Detailed description |
| category | ENUM | NOT NULL | Category (gift_card, cash, donation) |
| points_required | INTEGER | NOT NULL | Points cost to redeem |
| monetary_value | DECIMAL(10,2) | NOT NULL | Monetary value in USD |
| image_url | VARCHAR(255) | | URL to reward image |
| terms_and_conditions | TEXT | | Terms and restrictions |
| is_active | BOOLEAN | DEFAULT true, NOT NULL | Whether reward is currently available |
| inventory_count | INTEGER | | Available inventory (null for unlimited) |
| provider | ENUM | NOT NULL | Provider (paypal, tango_card, internal) |
| provider_details | JSONB | | Provider-specific configuration |
| created_at | TIMESTAMP | DEFAULT NOW(), NOT NULL | When reward was added |
| updated_at | TIMESTAMP | DEFAULT NOW(), NOT NULL | When reward was last updated |

**Indexes:**
- Primary Key: `id`
- Index: `category`
- Index: `points_required`
- Index: `is_active`
- Index: `provider`

### Redemptions Table

**Table Name:** `redemptions`

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | UUID | PRIMARY KEY | Unique identifier for redemption |
| user_id | UUID | FOREIGN KEY, NOT NULL | Reference to users table |
| reward_id | UUID | FOREIGN KEY, NOT NULL | Reference to rewards table |
| points_amount | INTEGER | NOT NULL | Points deducted for redemption |
| status | ENUM | DEFAULT 'pending', NOT NULL | Status (pending, processing, completed, failed) |
| delivery_details | JSONB | NOT NULL | Email, phone, or other delivery info |
| provider_reference | VARCHAR(100) | | Transaction ID from provider |
| created_at | TIMESTAMP | DEFAULT NOW(), NOT NULL | When redemption was requested |
| updated_at | TIMESTAMP | DEFAULT NOW(), NOT NULL | When redemption was last updated |
| completed_at | TIMESTAMP | | When redemption was fulfilled |
| notes | TEXT | | Internal or user-facing notes |

**Indexes:**
- Primary Key: `id`
- Foreign Key: `user_id` references `users(id)`
- Foreign Key: `reward_id` references `rewards(id)`
- Index: `status`
- Index: `created_at`
- Index: `completed_at`

## Database Optimization Strategies

1. **Partitioning**
   - Partition `receipts` table by date range (monthly)
   - Partition `points_transactions` table by date range (monthly)
   - Partition `store_visits` table by date range (monthly)

2. **Materialized Views**
   - User points summary with aggregated stats
   - Store popularity ranks based on visit frequency
   - Deal activation leaderboard for trending deals

3. **Caching Strategy**
   - Cache user points balance with TTL of 5 minutes
   - Cache active deals list with TTL of 1 hour
   - Cache store geofence data with TTL of 24 hours

4. **Data Retention Policy**
   - Keep receipt images for 90 days, then archive
   - Keep full receipt data for 3 years, then summarize
   - Keep points transactions indefinitely
   - Keep store visit data for 1 year

5. **Performance Considerations**
   - Add read replicas for heavy read operations
   - Implement connection pooling
   - Regular VACUUM and analyze operations
   - Implement database monitoring with query analysis
