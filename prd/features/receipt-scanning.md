# Core Receipt Scanning

## Overview

**Priority**: P0 (MVP)
**Dependencies**: User Authentication
**Estimated Development Time**: 3 developer-weeks

## Description
Users can scan any grocery receipt to earn base points, similar to Fetch Rewards. This is the core functionality of the app and must be highly reliable, fast, and user-friendly.

## Detailed Requirements

### Functional Requirements
1. Implement a camera-based receipt scanner using OCR to extract key details (store name, date, items purchased, total amount).
2. Award a minimum of 25 points per valid receipt, with bonus points for receipts containing affiliate deal items.
3. Validate receipts to ensure they are from supported grocery stores and not older than 7 days from the scan date.
4. Store receipt data securely in the app database.
5. Allow users to manually enter receipt information if OCR fails.
6. Provide image enhancement capabilities (contrast adjustment, etc.) for poor quality images.
7. Support batch scanning for multiple receipts.

### User Flow
1. User taps "Scan" on the home screen.
2. Camera opens with a simple overlay frame for receipt alignment.
3. App processes the image, shows a confirmation screen with points earned, and updates the balance.
4. If OCR fails, user is prompted to either retake the photo or manually enter receipt details.

### UI Details
- Button: Single large "Scan" button (green #00CC00, rounded, 80% screen width) centered on the home screen.
- Camera View: Full-screen with a semi-transparent rectangular frame and text "Align receipt here" in white, 16pt font.
- Confirmation Screen: White background, bold text "Receipt Accepted! +25 Points" (green, 20pt), "Back" button (gray, bottom right).
- Error Screen: White background, "We couldn't read your receipt" (black, 16pt), "Try Again" and "Enter Manually" buttons.
