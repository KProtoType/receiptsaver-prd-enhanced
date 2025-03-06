# Receipt Scanning (Continued)

## Acceptance Criteria

1. **Camera Functionality**
   - Camera launches within 2 seconds of tapping the "Scan" button
   - Camera overlay provides clear guidance for receipt alignment
   - Flash/torch can be toggled for low-light conditions
   - Multiple images can be captured for long receipts

2. **OCR Processing**
   - OCR successfully extracts store name, date, total amount with >95% accuracy
   - OCR extracts individual line items with >80% accuracy
   - Processing completes in <5 seconds on typical devices
   - System identifies duplicates to prevent fraud

3. **Receipt Validation**
   - System correctly identifies supported grocery stores
   - System validates receipt date within 7-day window
   - System detects and rejects non-grocery receipts
   - System prevents scanning the same receipt twice

4. **Point Awarding**
   - Base points (25) are awarded for every valid receipt
   - Additional points are awarded for matched affiliate deals
   - Points are immediately added to user balance
   - Receipt history shows all points earned

5. **Manual Entry**
   - Users can manually enter store name, date, and total amount
   - Manual entries are clearly marked in the database
   - Manual entries receive same base points but require admin review for deal matching

## Technical Implementation Details

### OCR Service Integration
- Primary: Google Cloud Vision API
- Fallback: Amazon Textract
- Custom post-processing to extract structured data (store, date, items, total)

### Receipt Database Schema
```json
{
  "id": "uuid",
  "user_id": "uuid",
  "created_at": "timestamp",
  "store_name": "string",
  "receipt_date": "date",
  "total_amount": "decimal",
  "item_count": "integer",
  "items": [{
    "name": "string",
    "price": "decimal",
    "quantity": "integer",
    "matched_deal_id": "uuid|null"
  }],
  "image_url": "string",
  "points_awarded": "integer",
  "status": "enum(processed, pending_review, rejected)",
  "ocr_confidence": "float",
  "is_manual_entry": "boolean"
}
```

### API Endpoints

**POST /receipts/scan**
```json
Request: {
  "image_base64": "string",
  "device_info": {
    "model": "string",
    "os": "string",
    "app_version": "string"
  }
}

Response: {
  "success": true,
  "receipt_id": "uuid",
  "points_awarded": 25,
  "matched_deals": [{
    "deal_id": "uuid",
    "product_name": "string",
    "bonus_points": 10
  }],
  "total_points": 35,
  "new_balance": 1250
}
```
