# Location-Based Scanning Nudge

## Overview

**Priority**: P2 (Enhancement)
**Dependencies**: Core Receipt Scanning
**Estimated Development Time**: 2 developer-weeks

## Description
The location-based scanning nudge feature detects when a user leaves a grocery store and sends a timely notification reminding them to scan their receipt. This feature optimizes the user experience by prompting users at the ideal moment in their shopping journey.

## Detailed Requirements

### Functional Requirements
1. Use geolocation services (GPS) to monitor user location and define geofences around a predefined list of grocery stores.
2. Trigger detection when the user exits the geofence (leaves the store), determined by a radius of 50 meters around the store's coordinates.
3. Send a push notification within 30 seconds of exit: "Just left [Store Name]? Scan your receipt now to earn rewards!"
4. Include a deep link to the "Scan" screen in the notification.
5. Allow users to opt-in or opt-out of location tracking via settings (default: opt-in with onboarding prompt).
6. Update grocery store database monthly via Google Places API or equivalent.
7. Handle edge cases like re-entry and recent scans to avoid duplicate notifications.

### User Flow
1. User enables location tracking during app onboarding (or later via settings).
2. User shops at a grocery store with location services enabled.
3. User exits the store, crossing the geofence boundary.
4. App detects exit and sends a push notification.
5. User taps the notification, opening the "Scan" screen directly.

### UI Details
- Notification: Simple text in system font, green "Scan Now" button on the right.
- Onboarding Prompt: White screen, black text "Allow location to get scan reminders?" (16pt), two buttons: "Yes" (green, left), "No" (gray, right).
- Settings Toggle: Standard switch control in app settings labeled "Receipt Scan Reminders".

## Acceptance Criteria

1. **Location Permission Management**
   - App clearly explains why location permission is needed
   - Users can enable/disable location tracking any time via settings
   - App handles permission denial gracefully with option to enable later
   - Background location usage complies with platform requirements

2. **Geofence Accuracy**
   - System correctly identifies at least 90% of supported grocery stores
   - Store entry is detected within 100 meters of true store location
   - Store exit is detected within 50 meters of exit point
   - System manages battery impact by optimizing location requests

3. **Notification Behavior**
   - Notification arrives within 30 seconds of store exit
   - Notification correctly identifies the store name
   - Tapping notification launches directly to scan screen
   - Multiple exits within 5 minutes only trigger one notification

4. **Edge Case Handling**
   - No notification if receipt already scanned from that store within past hour
   - Handles temporary loss of GPS signal appropriately
   - Works across device restarts (persists store entry state)
   - Manages low battery conditions appropriately

## Technical Implementation Details

### Geofencing Service
- Primary: Native iOS/Android geofencing APIs
- Supplemental: Google Places API for store database
- Custom location optimization to minimize battery drain

### Store Database Schema
```json
{
  "id": "uuid",
  "name": "string",
  "chain": "string",  // e.g., "Walmart", "Kroger"
  "address": "string",
  "city": "string",
  "state": "string",
  "postal_code": "string",
  "country": "string",
  "latitude": "float",
  "longitude": "float",
  "geofence_radius": "integer",  // in meters
  "supported": "boolean",
  "popularity_score": "float",  // for prioritization
  "last_updated": "timestamp"
}
```

### Visit Tracking Schema
```json
{
  "id": "uuid",
  "user_id": "uuid",
  "store_id": "uuid",
  "entered_at": "timestamp",
  "exited_at": "timestamp",
  "notification_sent": "boolean",
  "notification_time": "timestamp",
  "receipt_scanned": "boolean",
  "receipt_id": "uuid",
  "scan_time": "timestamp"
}
```

### API Endpoints

**GET /stores/nearby**
```json
Request Parameters: {
  "latitude": "float",
  "longitude": "float",
  "radius": "integer"  // in meters
}

Response: {
  "stores": [
    {
      "id": "uuid",
      "name": "Walmart Supercenter",
      "address": "123 Main St, San Francisco, CA",
      "latitude": 37.7749,
      "longitude": -122.4194,
      "distance": 325,  // meters from current location
      "supported": true
    },
    // More stores...
  ]
}
```

**POST /visits/entry**
```json
Request: {
  "store_id": "uuid",
  "entry_time": "timestamp",
  "latitude": "float",
  "longitude": "float"
}

Response: {
  "success": true,
  "visit_id": "uuid"
}
```

**POST /visits/exit**
```json
Request: {
  "visit_id": "uuid",
  "exit_time": "timestamp",
  "latitude": "float",
  "longitude": "float"
}

Response: {
  "success": true,
  "notification_sent": true,
  "should_suppress": false  // based on recent scans
}
```

## Error Handling and Edge Cases

### Error Cases
1. **Location Services Disabled**
   - Detection: System location permission check
   - Handling: Prompt user once per week max, provide clear instructions

2. **Unreliable GPS**
   - Detection: Low accuracy readings or gaps in data
   - Handling: Use last known location, cell tower triangulation as fallback

3. **Nearby Stores**
   - Detection: Multiple stores within 100m radius
   - Handling: Select store with highest confidence based on visit duration and movement pattern

4. **False Exit Detection**
   - Detection: Re-entry within 5 minutes
   - Handling: Cancel pending notifications, merge visit records

### Edge Cases
1. **Drive-through Visits**
   - Handling: Set minimum visit duration (2 minutes) to qualify for notification

2. **Shopping Mall with Multiple Stores**
   - Handling: Use store-specific geofences with priority system

3. **App Closed During Shopping**
   - Handling: Use system background geofence monitoring where available

4. **Device Battery Below 15%**
   - Handling: Reduce location monitoring frequency or pause temporarily

## Development Estimation

| Task | Estimated Time |
|------|----------------|
| Location permission management | 1-2 days |
| Geofencing implementation | 3-4 days |
| Store database integration | 2-3 days |
| Notification system | 1-2 days |
| Visit tracking logic | 2-3 days |
| Battery optimization | 1-2 days |
| Testing & refinement | 2-3 days |
| **Total** | **12-19 days (2.5-4 weeks)** |

## Feature Flag

Feature name: `enable_location_nudges`

Controls the entire location-based nudge feature. When disabled, location permissions are not requested and no geofencing occurs.

## Analytics

| Event | Properties | Description |
|-------|------------|-------------|
| `location_permission_granted` | source | User enables location services |
| `location_permission_denied` | source | User denies location services |
| `store_entry_detected` | store_id, accuracy | System detects user entering store |
| `store_exit_detected` | store_id, visit_duration | System detects user leaving store |
| `exit_notification_sent` | store_id, delay | Exit notification is delivered |
| `notification_clicked` | store_id, time_to_click | User taps on notification |
| `scan_from_notification` | store_id, time_to_scan | User scans receipt after tapping notification |
