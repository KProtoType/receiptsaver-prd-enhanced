# Receipt Scanning (Continued)

**POST /receipts/manual**
```json
Request: {
  "store_name": "string",
  "receipt_date": "date",
  "total_amount": "decimal",
  "items": [{
    "name": "string",
    "price": "decimal",
    "quantity": "integer"
  }]
}

Response: {
  "success": true,
  "receipt_id": "uuid",
  "points_awarded": 25,
  "new_balance": 1250,
  "status": "pending_review"
}
```

## Error Handling and Edge Cases

### Error Cases
1. **Blurry Image**
   - Detection: OCR confidence below 70%
   - Handling: Prompt user to recapture with guidance ("Hold steady and ensure good lighting")

2. **Network Failure During Upload**
   - Detection: API request timeout or network error
   - Handling: Cache image locally, retry automatically when connection returns

3. **Unsupported Receipt**
   - Detection: Store not in database or non-grocery
   - Handling: Inform user with specific reason ("We currently only support grocery stores")

4. **Duplicate Receipt**
   - Detection: Matching store/date/total in user history
   - Handling: Show error with previous scan date ("You've already scanned this receipt on [date]")

5. **OCR Service Unavailable**
   - Detection: OCR API returns error
   - Handling: Prompt for manual entry and log incident for engineering

### Edge Cases
1. **Multiple Receipts in One Image**
   - Handling: Detect multiple receipts and prompt user to capture each separately

2. **Handwritten Receipts**
   - Handling: Lower confidence threshold, flag for manual review, still award base points

3. **Non-English Receipts**
   - Handling: Support for Spanish, French, and Chinese initially, others flagged for review

4. **Giant Receipts**
   - Handling: Support stitching of multiple images for receipts longer than screen

5. **Digital Receipts (Email/Text)**
   - Handling: Support screenshot upload of digital receipts with same validation process

## Development Estimation

| Task | Estimated Time |
|------|----------------|
| Camera integration & UI | 3-4 days |
| OCR service integration | 3-4 days |
| Receipt validation logic | 2-3 days |
| Database & API implementation | 3-4 days |
| Points awarding & balance updates | 1-2 days |
| Error handling & edge cases | 2-3 days |
| Testing & refinement | 3-4 days |
| **Total** | **15-20 days (3-4 weeks)** |

## Feature Flag

Feature name: `enable_receipt_scanning`

Allows for gradual rollout or quick disabling if issues are detected. When disabled, the scan button remains but shows a "Coming Soon" message when tapped.

## Analytics

| Event | Properties | Description |
|-------|------------|-------------|
| `scan_initiated` | device_type, camera_type | User taps scan button |
| `scan_completed` | processing_time, ocr_confidence | OCR finishes processing |
| `scan_success` | points_awarded, store_name, receipt_total | Receipt is accepted |
| `scan_failure` | error_type, error_message | Receipt is rejected |
| `manual_entry` | reason | User opts for manual entry |
