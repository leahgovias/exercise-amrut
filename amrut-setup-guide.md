# Exercise AMRUT — Setup Guide

## Overview

The AMRUT app is a single HTML file (`amrut-app.html`) that uses **Firebase Firestore** as its real-time backend. All 200+ participants open the same file in a browser (hosted or local), and see live updates instantly.

---

## Step 1 — Create a Firebase Project

1. Go to [console.firebase.google.com](https://console.firebase.google.com)
2. Click **Add project** → name it (e.g., `exercise-amrut`)
3. Disable Google Analytics (not needed) → **Create project**

---

## Step 2 — Add a Web App

1. In the Firebase console, click the **</>** (Web) icon
2. Give the app a nickname (e.g., `AMRUT Web`)
3. Click **Register app**
4. You will see a `firebaseConfig` block like this:

```js
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "exercise-amrut.firebaseapp.com",
  projectId: "exercise-amrut",
  storageBucket: "exercise-amrut.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

5. Copy these values — you will paste them into `amrut-app.html`

---

## Step 3 — Enable Firestore

1. In the left sidebar, go to **Build → Firestore Database**
2. Click **Create database**
3. Choose **Start in test mode** (allows all reads/writes — fine for the exercise duration)
4. Select a location closest to India (e.g., `asia-south1` — Mumbai)
5. Click **Enable**

> **Security note:** Test mode rules expire after 30 days. After the exercise, go to Firestore → Rules and set `allow read, write: if false;` to lock it down.

---

## Step 4 — Configure the App

Open `amrut-app.html` in a text editor. Near the top of the `<script>` section, find:

```js
const FIREBASE_CONFIG = {
  apiKey:            "YOUR_API_KEY",
  authDomain:        "YOUR_PROJECT_ID.firebaseapp.com",
  projectId:         "YOUR_PROJECT_ID",
  storageBucket:     "YOUR_PROJECT_ID.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId:             "YOUR_APP_ID"
};

const CONTROL_PASSWORD = "AMRUT@Control2024";
```

Replace each `YOUR_*` value with your Firebase config values. Also set a strong `CONTROL_PASSWORD` that only your faculty team will know.

---

## Step 5 — Host the App

**Option A — Firebase Hosting (recommended, free)**

1. Install Node.js from [nodejs.org](https://nodejs.org)
2. Open Terminal and run:
   ```
   npm install -g firebase-tools
   firebase login
   firebase init hosting
   ```
3. Put `amrut-app.html` in the `public/` folder, renamed to `index.html`
4. Run `firebase deploy`
5. Your app will be live at `https://YOUR-PROJECT-ID.web.app`

**Option B — Share the HTML file directly**

Since the app is a single file, participants can open it locally in their browser. However, all participants must have the same copy of the file. You can:
- Email it to all participants before the exercise
- Share via a Google Drive link (File → Download to open)
- Host it on any static hosting service (Netlify, GitHub Pages, etc.)

> For a 200-person exercise, Firebase Hosting is the cleanest option — one URL for everyone.

---

## Step 6 — Prepare the Roster CSV

Create a CSV file with these exact column headers (case-insensitive):

```
Name,Rank,Service,Squadron
Rajesh Kumar,Colonel,Army,1
Priya Sharma,Commander,Navy,2
Arun Mehta,Wing Commander,Air Force,3
...
```

- **Name** — full name
- **Rank** — e.g., Colonel, Commander, Wing Commander, Brigadier
- **Service** — Army / Navy / Air Force / Coast Guard / IFS / IPS / IAS
- **Squadron** — a number from 1 to 12

---

## Step 7 — Load the Roster

1. Open the app and log in as **Control** using your Control Password
2. Click the **👥 Roster** tab
3. Click **Choose File** and select your CSV
4. Review the preview count
5. Click **📤 Upload Roster**

Once uploaded, squadron members can log in by selecting their squadron and name from the dropdown.

---

## Day-of-Exercise Flow

### Before the exercise begins

1. Log in as Control
2. Go to **Roster** tab → confirm all 12 squadrons show members
3. Go to **Situation** tab → set Exercise Status to **Standby**

### When the exercise starts

1. Faculty presents Crisis 1 via Webex slides as usual
2. In the **Situation** tab, type the crisis title, description, and requirements → click **Publish Situation**  
   → All squadron members instantly see it in their Live Updates feed
3. Set a **Countdown Timer** using the ⏱ button — squadrons see it live in the top bar

### During working time

- Send mid-exercise updates via **📢 Broadcast** tab
- Check **✅ Attendance** tab for live counts across all 12 squadrons
- Respond to squadron queries in the **❓ Queries** tab (badge shows pending count)

### Repeat for Crisis 2, 3, 4

Re-use the **Situation** tab for each new crisis. The feed accumulates — squadrons always see the full history.

---

## What Each Role Sees

### Control Panel (faculty)
| Tab | Function |
|-----|----------|
| 📋 Situation | Post/update crisis title, description, requirements, exercise status |
| 📢 Broadcast | Send announcements to all or specific squadrons |
| ✅ Attendance | Live grid of 12 squadrons with present/total counts; drill into individual squadron |
| ❓ Queries | See all squadron questions; respond inline; pending badge shows urgency |
| 👥 Roster | Upload CSV roster; view current roster by squadron |

### Squadron Member View
| Section | Function |
|---------|----------|
| Live Updates | Real-time feed of situations and announcements from Control |
| Attendance | One-tap attendance marking (confirms with timestamp) |
| Ask Control | Submit a question; see Control's response appear live |
| Timer | Countdown visible in top bar |

---

## Firestore Security Rules (for reference)

Paste into Firebase → Firestore → Rules during the exercise:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.time < timestamp.date(2025, 12, 31);
    }
  }
}
```

Change the date to one day after your exercise. After it ends, update to `if false` to lock the database.

---

## Troubleshooting

**"Firebase not configured" warning on login screen**  
→ Open `amrut-app.html` and fill in the `FIREBASE_CONFIG` values from Step 4.

**Squadron member can't find their name**  
→ Check the roster CSV: Name and Squadron columns must be present. Re-upload if needed.

**Real-time updates not appearing**  
→ Check browser console for Firestore permission errors. Ensure Firestore is in test mode and the project ID matches.

**200 users — will Firebase handle this?**  
→ Yes. Firebase free tier (Spark) supports 50,000 reads/day and 20,000 writes/day, well within range for a 3-day exercise. Concurrent connections are also well within limits.
