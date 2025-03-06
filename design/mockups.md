# Screen Mockups

This document provides detailed mockups for the key screens of the ReceiptSaver app. These mockups are meant to guide the development team in implementing the UI with precision.

## 1. Home Screen (Receipt Scanning)

```
┌─────────────────────────────────────────┐
│                                         │
│                                         │
│                                         │
│                                         │
│          [User Profile Icon]            │
│                                         │
│             "2,500 Points"              │
│           (Green, 20pt font)            │
│                                         │
│                                         │
│                                         │
│        ┌───────────────────────┐        │
│        │         SCAN          │        │
│        │    (Green Button)     │        │
│        └───────────────────────┘        │
│                                         │
│                                         │
│                                         │
│                                         │
│                                         │
│ [Scan Tab]    [Deals Tab]   [Rewards Tab]│
└─────────────────────────────────────────┘
```

**Key Elements:**
- Large "SCAN" button (80% width, green #00CC00, white text)
- Points balance displayed prominently (green text, 20pt font)
- User profile icon in top right for account access
- Bottom navigation with 3 tabs (active tab highlighted in green)

## 2. Camera Screen (Receipt Capture)

```
┌─────────────────────────────────────────┐
│ [Cancel]                       [Flash]  │
│                                         │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │                                 │    │
│  │                                 │    │
│  │        "Align receipt here"     │    │
│  │         (White, 16pt font)      │    │
│  │                                 │    │
│  │                                 │    │
│  │                                 │    │
│  │                                 │    │
│  └─────────────────────────────────┘    │
│                                         │
│                                         │
│                                         │
│                                         │
│             [Camera Button]             │
│                                         │
└─────────────────────────────────────────┘
```

**Key Elements:**
- Full-screen camera view with semi-transparent guide frame
- "Align receipt here" text inside the frame (white text)
- Camera capture button at bottom center
- Cancel button (top left) and flash toggle (top right)

## 3. Receipt Processing Screen

```
┌─────────────────────────────────────────┐
│                                         │
│                                         │
│                                         │
│            [Scanning Animation]         │
│                                         │
│                                         │
│          "Processing Receipt..."        │
│            (Black, 18pt font)           │
│                                         │
│                                         │
│            "Please wait..."             │
│            (Gray, 14pt font)            │
│                                         │
│                                         │
│          [Circular Progress Bar]        │
│                                         │
│                                         │
│                                         │
│                                         │
└─────────────────────────────────────────┘
```

**Key Elements:**
- Scanning animation (receipt with moving scan line)
- "Processing Receipt..." text (black, 18pt)
- "Please wait..." subtext (gray, 14pt)
- Circular progress indicator

## 4. Success Confirmation Screen

```
┌─────────────────────────────────────────┐
│                                         │
│                                         │
│          [Success Checkmark Icon]       │
│                                         │
│                                         │
│           "Receipt Accepted!"           │
│            (Green, 20pt font)           │
│                                         │
│                                         │
│               "+25 Points"              │
│            (Green, 24pt font)           │
│                                         │
│      ┌─────────────────────────┐        │
│      │  "Coca-Cola +10 Points" │        │
│      │   (Matched Deal Item)   │        │
│      └─────────────────────────┘        │
│                                         │
│      "New Balance: 2,535 Points"        │
│                                         │
│                                         │
│      [View Receipt]     [Done Button]   │
└─────────────────────────────────────────┘
```

**Key Elements:**
- Large checkmark success icon (green)
- "Receipt Accepted!" headline (green, 20pt)
- Points awarded display (+25 Points in green, 24pt)
- Matched deals list with bonus points
- Updated points balance
- "Done" button (green) and "View Receipt" option (text link)

## 5. Deals Tab

```
┌─────────────────────────────────────────┐
│                                         │
│  [Search Bar - "Search deals"]          │
│                                         │
│  [All] [Grocery] [Beverages] [Snacks]   │
│  (Horizontal scrolling category pills)   │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │ Coca-Cola 12oz                  │    │
│  │ $0.50 off (10 points)    [+Add] │    │
│  │ Expires 3/10/25                 │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │ Lays Potato Chips               │    │
│  │ $1.00 off (20 points)    [+Add] │    │
│  │ Expires 3/15/25                 │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │ Bounty Paper Towels             │    │
│  │ $2.00 off (40 points)    [+Add] │    │
│  │ Expires 3/20/25                 │    │
│  └─────────────────────────────────┘    │
│                                         │
│ [Scan Tab]    [Deals Tab]   [Rewards Tab]│
└─────────────────────────────────────────┘
```

**Key Elements:**
- Search bar at top
- Horizontal scrolling category filters
- Deal cards with product name, value, expiration
- "+Add" button on each card (green)
- Bottom navigation (Deals tab highlighted)

## 6. Rewards Tab

```
┌─────────────────────────────────────────┐
│                                         │
│           "2,535 Points"                │
│         (Green, 24pt font)              │
│                                         │
│  [All] [Gift Cards] [Cash] [Donations]  │
│  (Horizontal scrolling category pills)   │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │         [Amazon Icon]           │    │
│  │      $5 Amazon Gift Card        │    │
│  │           5,000 Points          │    │
│  │          [Redeem Button]        │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │         [PayPal Icon]           │    │
│  │         $3 PayPal Cash          │    │
│  │           3,000 Points          │    │
│  │          [Redeem Button]        │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │         [Target Icon]           │    │
│  │       $10 Target Gift Card      │    │
│  │          10,000 Points          │    │
│  │          [Redeem Button]        │    │
│  └─────────────────────────────────┘    │
│                                         │
│ [Scan Tab]    [Deals Tab]   [Rewards Tab]│
└─────────────────────────────────────────┘
```

**Key Elements:**
- Points balance displayed prominently (green, 24pt)
- Horizontal scrolling reward categories
- Reward cards with logo, description, and point cost
- "Redeem" button on each card (green, disabled if insufficient points)
- Bottom navigation (Rewards tab highlighted)

## 7. Location Permission Screen

```
┌─────────────────────────────────────────┐
│                                         │
│                                         │
│           [Location Pin Icon]           │
│                                         │
│                                         │
│      "Scan Receipts at the Right Time"  │
│         (Black, 20pt font)              │
│                                         │
│                                         │
│    "Allow location access to receive    │
│     reminders when you leave stores.    │
│     We'll only use your location to     │
│     send timely notifications."         │
│     (Gray, 16pt font)                   │
│                                         │
│                                         │
│    [Allow Location]    [Not Now]        │
│    (Green Button)      (Gray Button)    │
│                                         │
│                                         │
└─────────────────────────────────────────┘
```

**Key Elements:**
- Location pin icon
- Clear headline explaining the feature
- Explanatory text about location usage
- "Allow Location" button (green, primary)
- "Not Now" button (gray, secondary)

## 8. Deal Detail Screen

```
┌─────────────────────────────────────────┐
│  [Back]                                 │
│                                         │
│         [Product Image]                 │
│                                         │
│         "Coca-Cola 12oz"                │
│         (Black, 20pt font)              │
│                                         │
│         "$0.50 off (10 Points)"         │
│         (Green, 18pt font)              │
│                                         │
│         "Expires 3/10/25"               │
│         (Gray, 14pt font)               │
│                                         │
│  Details:                               │
│  - Valid at: Walmart, Target, Kroger    │
│  - Limit: 5 per receipt                 │
│  - Restrictions: 12oz cans only         │
│  - Source: Ibotta                       │
│                                         │
│                                         │
│         [Add to My Deals]               │
│         (Green Button)                  │
│                                         │
└─────────────────────────────────────────┘
```

**Key Elements:**
- Back button at top left
- Product image
- Product name (black, 20pt)
- Deal value (green, 18pt)
- Expiration date (gray, 14pt)
- Detailed terms and conditions
- "Add to My Deals" button (green, full width)
