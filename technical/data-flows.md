# Data Flow Diagrams

This document provides data flow diagrams for the key processes in the ReceiptSaver app. These diagrams help developers understand the sequence of operations and data transformations.

## 1. Receipt Scanning Process

```mermaid
sequenceDiagram
    participant User
    participant App
    participant API
    participant OCR as OCR Service
    participant DB as Database
    
    User->>App: Captures receipt image
    App->>App: Preprocesses image (crop, enhance)
    App->>API: POST /receipts/scan (image_base64)
    API->>OCR: Send image for processing
    OCR->>API: Return structured text data
    API->>API: Extract store, date, items, total
    API->>API: Validate receipt (date, store)
    API->>DB: Check for duplicates
    API->>DB: Query for matching deals
    API->>DB: Save receipt data
    API->>DB: Award points to user
    API->>App: Return receipt ID, points, matches
    App->>User: Display success & points awarded
```

## 2. Deal Matching Process

```mermaid
flowchart TD
    A[Receipt Data] --> B{Extract Line Items}
    B --> C[Normalize Product Names]
    C --> D{For Each Line Item}
    
    D --> E[Exact Match Search]
    E --> F{Match Found?}
    
    F -->|Yes| G[Apply Deal]
    F -->|No| H[Fuzzy Match Search]
    
    H --> I{Match > 85%?}
    I -->|Yes| G
    I -->|No| J[Brand Match Search]
    
    J --> K{Brand Match?}
    K -->|Yes| G
    K -->|No| L[No Deal Applied]
    
    G --> M[Calculate Bonus Points]
    L --> N[End Item Processing]
    M --> N
    
    N --> O{More Items?}
    O -->|Yes| D
    O -->|No| P[Sum Total Bonus Points]
    
    P --> Q[Add Base Points]
    Q --> R[Update User Balance]
```

## 3. User Authentication Flow

```mermaid
sequenceDiagram
    participant User
    participant App
    participant Auth as Auth Service
    participant API
    participant DB as Database
    
    User->>App: Enter email/password or social login
    
    alt Email/Password Login
        App->>Auth: Authenticate credentials
        Auth->>DB: Verify user credentials
        DB->>Auth: Verification result
    else Social Login
        App->>Auth: Forward provider token
        Auth->>Auth: Verify with provider
        Auth->>DB: Find or create user
    end
    
    Auth->>Auth: Generate JWT token
    Auth->>API: User authenticated
    API->>DB: Get user profile data
    DB->>API: User profile
    API->>App: Auth token + user data
    App->>App: Store token securely
    App->>User: Navigate to home screen
```

## 4. Location-Based Notification Flow

```mermaid
sequenceDiagram
    participant User
    participant App
    participant OS as Mobile OS
    participant API
    participant DB as Database
    
    App->>OS: Register store geofences
    OS->>App: Background monitoring
    User->>User: Enters store
    OS->>App: Geofence entry event
    App->>API: POST /visits/entry
    API->>DB: Record store entry
    
    User->>User: Shops and exits store
    OS->>App: Geofence exit event
    App->>App: Check last receipt scan time
    
    alt No recent scan from this store
        App->>API: POST /visits/exit
        API->>DB: Record store exit
        API->>API: Check notification eligibility
        API->>App: Confirm notification
        App->>User: Send push notification
        User->>App: Tap notification
        App->>App: Open receipt scanning screen
    else Recent scan exists
        App->>API: POST /visits/exit (no notification)
        API->>DB: Record store exit (no notification)
    end
```

## 5. Reward Redemption Flow

```mermaid
sequenceDiagram
    participant User
    participant App
    participant API
    participant DB as Database
    participant Provider as Reward Provider
    
    User->>App: Browse rewards catalog
    App->>API: GET /rewards
    API->>DB: Query available rewards
    DB->>API: Rewards catalog
    API->>App: Display rewards
    
    User->>App: Select reward to redeem
    App->>App: Verify sufficient points
    App->>API: POST /rewards/redeem
    API->>DB: Verify points balance
    API->>DB: Deduct points (status: pending)
    
    alt Gift Card Reward
        API->>Provider: Request gift card code
        Provider->>API: Return digital code
        API->>DB: Save redemption details
        API->>App: Redemption success + code
        App->>User: Display gift card code
    else PayPal Cash
        API->>Provider: Request PayPal transfer
        Provider->>API: Confirm transfer initiated
        API->>DB: Save redemption details
        API->>App: Redemption success + status
        App->>User: Display transfer status
    end
```

## 6. Data Architecture Overview

```mermaid
flowchart TD
    subgraph Client
        A[Mobile App] <--> B[Local Storage]
        A <--> C[Device Camera]
        A <--> D[Location Services]
        A <--> E[Push Notifications]
    end
    
    subgraph Backend
        F[API Gateway] <--> G[Auth Service]
        F <--> H[Receipt Service]
        F <--> I[Deal Service]
        F <--> J[Reward Service]
        F <--> K[Location Service]
        F <--> L[Notification Service]
    end
    
    subgraph External
        M[OCR API]
        N[Affiliate APIs]
        O[Payment Providers]
        P[Places API]
    end
    
    subgraph Data
        Q[(User DB)]
        R[(Receipt DB)]
        S[(Deal DB)]
        T[(Store DB)]
    end
    
    A <--> F
    H <--> M
    I <--> N
    J <--> O
    K <--> P
    
    G <--> Q
    H <--> R
    I <--> S
    K <--> T
```

## 7. Points Calculation Logic

```mermaid
flowchart TD
    A[Start Receipt Processing] --> B[Award Base 25 Points]
    B --> C{Detect Eligible Items}
    
    C --> D[Match Against Active Deals]
    D --> E{Matches Found?}
    
    E -->|Yes| F[For Each Matched Item]
    E -->|No| L[No Bonus Points]
    
    F --> G{First Purchase?}
    G -->|Yes| H[Apply First-Purchase Multiplier]
    G -->|No| I[Standard Points]
    
    H --> J[Calculate Bonus Points]
    I --> J
    
    J --> K{More Matched Items?}
    K -->|Yes| F
    K -->|No| M[Sum All Bonus Points]
    
    L --> N[Calculate Total Points]
    M --> N
    
    N --> O[Update User Balance]
    O --> P[End Process]
```

These diagrams provide detailed visual representations of the key data flows within the ReceiptSaver app and should help developers understand the system architecture and implementation requirements.
