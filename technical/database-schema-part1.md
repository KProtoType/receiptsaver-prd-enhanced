# Database Schema (Part 1)

This document outlines the complete database schema for the ReceiptSaver app, including all tables, relationships, and indexes. This schema is designed to support all the features described in the PRD while maintaining performance and scalability.

## Entity Relationship Diagram

```mermaid
erDiagram
    USERS {
        uuid id PK
        string email UK
        string password_hash
        boolean email_verified
        string display_name
        string phone_number
        string profile_image_url
        integer points_balance
        integer lifetime_points
        timestamp created_at
        timestamp updated_at
        timestamp last_login
        enum status
    }
    
    LINKED_ACCOUNTS {
        uuid id PK
        uuid user_id FK
        enum provider
        string provider_id
        timestamp linked_at
    }
    
    USER_SETTINGS {
        uuid id PK
        uuid user_id FK
        boolean biometric_login_enabled
        boolean location_tracking_enabled
        boolean receipt_reminders_enabled
        boolean deal_alerts_enabled
        boolean reward_updates_enabled
        timestamp updated_at
    }
    
    RECEIPTS {
        uuid id PK
        uuid user_id FK
        timestamp created_at
        string store_name
        date receipt_date
        decimal total_amount
        integer item_count
        jsonb items
        string image_url
        integer points_awarded
        enum status
        float ocr_confidence
        boolean is_manual_entry
    }
    
    POINTS_TRANSACTIONS {
        uuid id PK
        uuid user_id FK
        enum transaction_type
        integer amount
        enum source
        uuid source_id
        string description
        integer balance_after
        timestamp created_at
        enum status
    }
    
    DEALS {
        uuid id PK
        string external_id
        enum affiliate_source
        string product_name
        string description
        string brand
        string category
        enum reward_type
        decimal reward_value
        integer points_value
        string image_url
        string terms
        timestamp start_date
        timestamp end_date
        string[] stores
        integer required_quantity
        integer limit_per_receipt
        integer limit_per_user
        string barcode
        timestamp created_at
        timestamp updated_at
        boolean is_featured
        float popularity_score
    }
    
    USER_DEALS {
        uuid id PK
        uuid user_id FK
        uuid deal_id FK
        timestamp activated_at
        enum status
        integer used_count
        timestamp last_used_at
    }
    
    REWARDS {
        uuid id PK
        string title
        string description
        enum category
        integer points_required
        decimal monetary_value
        string image_url
        string terms_and_conditions
        boolean is_active
        integer inventory_count
        enum provider
        jsonb provider_details
        timestamp created_at
        timestamp updated_at
    }
    
    REDEMPTIONS {
        uuid id PK
        uuid user_id FK
        uuid reward_id FK
        integer points_amount
        enum status
        jsonb delivery_details
        string provider_reference
        timestamp created_at
        timestamp updated_at
        timestamp completed_at
        string notes
    }
    
    STORES {
        uuid id PK
        string name
        string chain
        string address
        string city
        string state
        string postal_code
        string country
        float latitude
        float longitude
        integer geofence_radius
        boolean supported
        float popularity_score
        timestamp last_updated
    }
    
    STORE_VISITS {
        uuid id PK
        uuid user_id FK
        uuid store_id FK
        timestamp entered_at
        timestamp exited_at
        boolean notification_sent
        timestamp notification_time
        boolean receipt_scanned
        uuid receipt_id FK
        timestamp scan_time
    }
    
    USERS ||--o{ LINKED_ACCOUNTS : "has"
    USERS ||--|| USER_SETTINGS : "has"
    USERS ||--o{ RECEIPTS : "scans"
    USERS ||--o{ POINTS_TRANSACTIONS : "earns/spends"
    USERS ||--o{ USER_DEALS : "activates"
    USERS ||--o{ REDEMPTIONS : "redeems"
    USERS ||--o{ STORE_VISITS : "visits"
    
    DEALS ||--o{ USER_DEALS : "activated as"
    REWARDS ||--o{ REDEMPTIONS : "redeemed as"
    STORES ||--o{ STORE_VISITS : "location of"
    RECEIPTS ||--o{ POINTS_TRANSACTIONS : "generates"
    RECEIPTS ||--o{ STORE_VISITS : "associated with"
```
