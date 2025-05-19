# 📘 Appointment Bot – Google Calendar Integration Guide

This guide explains how to set up and connect the Google Calendar API with your appointment bot, using exported Webex Connect flows.

---

## 🔧 Part 1: Google Calendar API Setup

### ✅ Step 1: Create a Google Cloud Project

1. Visit: [Google Cloud Console](https://console.cloud.google.com/)
2. Click **"Select project"** → **"New Project"**
3. Name it (e.g., `AppointmentBot`) and click **Create**

### ✅ Step 2: Enable Google Calendar API

1. Go to **APIs & Services > Library**
2. Search for **Google Calendar API**
3. Click **Enable**

### ✅ Step 3: Create OAuth Credentials

1. Go to **APIs & Services > Credentials**
2. Click **Create Credentials > OAuth Client ID**
3. Choose **Web application**
4. Under "Authorized redirect URIs", add:
   ```
   https://oauth.pstmn.io/v1/callback
   ```
5. Save the **Client ID** and **Client Secret**

### ✅ Step 4: Get Tokens (using Postman or browser)

1. Visit in browser:
   ```
   https://accounts.google.com/o/oauth2/v2/auth?client_id=YOUR_CLIENT_ID
   &redirect_uri=https://oauth.pstmn.io/v1/callback
   &response_type=code
   &scope=https://www.googleapis.com/auth/calendar
   &access_type=offline
   &prompt=consent
   ```
2. Copy the `code` from redirect
3. Exchange the code in Postman:

   **POST** `https://oauth2.googleapis.com/token`

   **Body (x-www-form-urlencoded):**
   ```
   client_id=YOUR_CLIENT_ID
   client_secret=YOUR_CLIENT_SECRET
   code=AUTH_CODE
   redirect_uri=https://oauth.pstmn.io/v1/callback
   grant_type=authorization_code
   ```

4. Save `access_token` and `refresh_token`

### ✅ Step 5: Refresh Token

**POST** `https://oauth2.googleapis.com/token`

**Body:**
```
client_id=YOUR_CLIENT_ID
client_secret=YOUR_CLIENT_SECRET
refresh_token=YOUR_REFRESH_TOKEN
grant_type=refresh_token
```

---

## 🔁 Part 2: Google Calendar API Calls

### ✅ Create Appointment

**POST** `https://www.googleapis.com/calendar/v3/calendars/primary/events`

**Headers:**
```
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

**Body:**
```json
{
  "summary": "Doctor Appointment",
  "location": "Downtown Clinic",
  "description": "Annual check-up. Phone: 3057605714",
  "start": {
    "dateTime": "2025-05-14T10:00:00-04:00",
    "timeZone": "America/New_York"
  },
  "end": {
    "dateTime": "2025-05-14T11:00:00-04:00",
    "timeZone": "America/New_York"
  },
  "attendees": [{ "email": "patient@example.com" }],
  "reminders": {
    "useDefault": false,
    "overrides": [
      { "method": "email", "minutes": 60 },
      { "method": "popup", "minutes": 10 }
    ]
  }
}
```

### ✅ Check Appointments by Phone

**GET**
```
https://www.googleapis.com/calendar/v3/calendars/primary/events?
maxResults=10&orderBy=startTime&singleEvents=true&
timeMin=2025-05-14T00:00:00Z&q=3057605714
```

### ✅ Delete Appointment

**DELETE**
```
https://www.googleapis.com/calendar/v3/calendars/primary/events/EVENT_ID
```

### ✅ Get Busy Slots (free/busy query)

**POST** `https://www.googleapis.com/calendar/v3/freeBusy`

**Headers:**
```
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

**Body:**
```json
{
  "timeMin": "2025-05-13T00:00:00Z",
  "timeMax": "2025-08-11T00:00:00Z",
  "timeZone": "America/New_York",
  "items": [{ "id": "primary" }]
}
```

---

## 🧠 Tips for Integration

- Always refresh the token before making calendar calls
- Use Evaluate nodes to:
  - Format `dateTime` in RFC3339
  - Parse `freeBusy` results
- Propose 3 available slots using AI + free slots
- Store user contact info (email/phone) in description for easy search

---

Feel free to include this file in your GitHub or with your exported Webex Connect bot flows.