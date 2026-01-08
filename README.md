# Livable
````
# Project: Citizens for Livable Indian Cities
## Module: Citizen Reporting & Engagement Portal (The "Voice" Engine)
**Version:** 1.0
**Status:** Draft Specification
**Target City:** Scalable (Pilot: City "Aaaa")

---

## 1. Overview
This module is the heart of the website. It allows citizens to interact with the platform in two distinct modes:
1.  **Report an Issue (Complaint):** Flagging problems like uncollected trash, sewage leaks, or pollution.
2.  **Appreciate Good Work (Praise):** Highlighting effective governance, clean areas, or responsive councilors.

**Core Philosophy:** Constructive engagement. We do not just want to list problems; we want to reinforce positive behavior by officials while holding them accountable for gaps.

---

## 2. User Experience Flow

1.  **Entry Point:** User clicks the **"Raise Your Voice"** floating button on their City Chapter homepage.
2.  **Mode Selection:** A toggle switch asks: *What do you want to do today?*
    * 🔴 **Fix This** (Complaint)
    * 🟢 **Appreciate This** (Praise)
3.  **The Form:** Dynamic fields appear based on the selection.
4.  **Verification:** OTP verification (to prevent spam).
5.  **Submission:** Data is processed, Geotagged, and placed on the public dashboard.

---

## 3. The Submission Form (Field Specifications)

The form must be mobile-responsive.

### Section A: Mode Selection
* **UI Element:** Large Toggle / Tab
* **Options:** `Report Issue` (Default) | `Give Praise`

### Section B: The Evidence (Image Upload)
* **Input Type:** File Upload (Camera/Gallery)
* **Constraints:**
    * Max 3 images.
    * Allowed formats: JPG, PNG, HEIC.
    * **Max size:** 5MB (Auto-compress on client-side if possible).
* **Metadata Extraction (Backend):**
    * System must attempt to extract GPS coordinates (Latitude/Longitude) and Timestamp from the image EXIF data automatically.

### Section C: Location Details
* **Auto-Detect:** "Use My Current Location" button (HTML5 Geolocation).
* **Manual Override:**
    * *Pin Drop Map:* Google Maps/OpenStreetMap embed to refine location.
    * *Landmark:* Text field (e.g., "Near Aaaa Main Market Gate 2").
    * *Ward Number (Optional):* Dropdown list specific to the city.

### Section D: Category & Details

#### If Mode == "Report Issue" (Complaint)
| Field Label | Type | Options / Validation |
| :--- | :--- | :--- |
| **Category** | Dropdown | • Garbage/Trash (Solid Waste)<br>• Sewage/Drainage<br>• Air Pollution (Burning/Construction)<br>• Water Supply<br>• Encroachment/Illegal Parking<br>• Road Condition (Potholes) |
| **Severity** | Slider/Radio | • Annoying (Low)<br>• Bad (Medium)<br>• Hazardous/Urgent (High) |
| **Description** | Text Area | "Describe the issue..." (Max 500 chars) |

#### If Mode == "Give Praise" (Praise)
| Field Label | Type | Options / Validation |
| :--- | :--- | :--- |
| **Category** | Dropdown | • Clean Street<br>• Regular Water Supply<br>• Well-maintained Park<br>• Prompt Action on Complaint<br>• Good Street Lighting |
| **Whom to Thank?** | Dropdown | • Local Councilor (Ward Member)<br>• Sanitation Workers<br>• Traffic Police<br>• General Municipality |
| **Description** | Text Area | "Tell us what they did right..." (Max 500 chars) |

### Section E: User Details
* **Name:** Text Field.
* **Anonymous Toggle:** Checkbox ("Keep my name hidden from public dashboard").
    * *Note: Name is required for backend verification but hidden from frontend if checked.*
* **Mobile Number:** Numeric (10 digits).
    * *Action:* Triggers OTP for verification.

---

## 4. Technical Logic & Data Structure

### A. Privacy & Anonymity
* **Public View:** If the user checks "Anonymous," the frontend displays "Concerned Citizen" instead of their name.
* **Official View:** Authorities with a login can request contact details only if the user consents (Need a checkbox: *"Allow officials to contact me for resolution"*).

### B. Image Processing Requirements
1.  **Compression:** Images must be compressed server-side to save bandwidth.
2.  **Watermarking:** Automatically overlay the timestamp and "Citizens for Livable Cities" logo on the uploaded photo to prevent misuse.

### C. JSON Data Schema (Example Payload)
This is how the data should be sent to the database.

```json
{
  "submission_id": "SUB_20251024_001",
  "city_chapter": "Aaaa",
  "timestamp": "2025-10-24T14:30:00Z",
  "type": "COMPLAINT", // or "PRAISE"
  "user": {
    "name": "Rahul Verma",
    "phone_hash": "a1b2c3d4...", 
    "is_anonymous": true
  },
  "location": {
    "lat": 28.7041,
    "long": 77.1025,
    "address": "Sector 4, Near Community Center",
    "ward_id": "WD_12"
  },
  "content": {
    "category": "GARBAGE_COLLECTION",
    "severity": "HIGH",
    "description": "Garbage truck has not come for 3 days. Piles accumulating.",
    "images": [
      "url_to_image1.jpg", 
      "url_to_image2.jpg"
    ]
  },
  "status": {
    "current_state": "OPEN", // OPEN, ACKNOWLEDGED, RESOLVED
    "official_response": null
  }
}
````

---

## 5. The "Wall of Fame" vs. "Wall of Shame"

The submitted data populates two sections on the website:

1. **The Live Action Map (For Complaints):**
    
    - Red pins for open issues.
        
    - Green pins for resolved issues.
        
    - _Feature:_ "Me Too" button. Other users can click "Me Too" on an existing complaint to increase its priority score without creating a duplicate post.
        
2. **The Leaderboard (For Praise):**
    
    - Displays the "Top Performing Wards" based on praise submissions.
        
    - Displays "Star Councilors" who have received the most positive feedback.
        
    - _Goal:_ Gamify good governance.
        

---

## 6. Developer Checklist

- [ ] Implement Geolocation API.
    
- [ ] Set up S3 bucket (or equivalent) for image storage with resizing rules.
    
- [ ] Build OTP verification integration (SMS gateway).
    
- [ ] Create the toggle logic for Complaint vs. Praise forms.
    
- [ ] Ensure EXIF data stripping (privacy) but keep coordinates for internal mapping.
