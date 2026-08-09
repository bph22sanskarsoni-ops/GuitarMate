# GuitarMate — Firebase + GitHub Setup Guide

## Overview
Is guide ko follow karke GuitarMate live hoga:
- Firebase pe → Auth (Login/Signup) + Firestore (Songs cloud sync)
- GitHub pe → Code store + auto-deploy

---

## PART 1 — Firebase Project Banana

### Step 1: Firebase Console kholo
1. `console.firebase.google.com` pe jao
2. **"Add project"** click karo
3. Project name: `guitarmate` (ya jo chahte ho)
4. Google Analytics: **disable** karo (zaroorat nahi)
5. **"Create project"** click karo → wait karo

---

### Step 2: Web App Register karo
1. Project dashboard pe aakar **`</>`** (Web) icon click karo
2. App nickname: `GuitarMate Web`
3. **"Firebase Hosting"** checkbox ✅ tick karo
4. **"Register app"** click karo
5. Ek config dikhega kuch aisa:

```js
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "guitarmate-xxxxx.firebaseapp.com",
  projectId: "guitarmate-xxxxx",
  storageBucket: "guitarmate-xxxxx.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef"
};
```

6. **Isko copy karke** `public/firebase-config.js` mein paste karo:

```js
// public/firebase-config.js
window.FIREBASE_CONFIG = {
  apiKey:            "AIzaSy...",         // yahan apna paste karo
  authDomain:        "guitarmate-xxxxx.firebaseapp.com",
  projectId:         "guitarmate-xxxxx",
  storageBucket:     "guitarmate-xxxxx.appspot.com",
  messagingSenderId: "123456789",
  appId:             "1:123456789:web:abcdef"
};
```

---

### Step 3: Authentication Enable karo
1. Firebase Console → Left sidebar → **Authentication**
2. **"Get started"** click karo
3. **Sign-in method** tab pe jao
4. **Email/Password** → Enable ✅ → Save
5. **Google** → Enable ✅ → Project support email select karo → Save

---

### Step 4: Firestore Database banana
1. Firebase Console → Left sidebar → **Firestore Database**
2. **"Create database"** click karo
3. **"Start in production mode"** select karo → Next
4. Location: `asia-south1` (India ke liye best) → Done
5. Database create ho jaayegi

#### Firestore Rules set karo:
1. Firestore → **Rules** tab
2. Poora existing content delete karo
3. Yeh paste karo:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/songs/{songId} {
      allow read, write: if request.auth != null
                         && request.auth.uid == userId;
    }
  }
}
```

4. **Publish** click karo

---

## PART 2 — GitHub Repository Banana

### Step 5: GitHub pe repo banao
1. `github.com` pe jao → **"New repository"**
2. Repository name: `guitarmate`
3. **Public** select karo
4. **"Create repository"** click karo

### Step 6: Code upload karo
PC pe terminal/command prompt kholo:

```bash
# Is folder mein jao jahan project hai
cd guitarmate-firebase

# Git initialize karo
git init
git add .
git commit -m "Initial commit - GuitarMate app"

# GitHub se connect karo (apna username daalo)
git remote add origin https://github.com/APNA_USERNAME/guitarmate.git
git branch -M main
git push -u origin main
```

---

## PART 3 — GitHub Actions Secret Setup

### Step 7: Firebase Service Account key banana
1. Firebase Console → ⚙️ **Project Settings** → **Service accounts** tab
2. **"Generate new private key"** click karo
3. JSON file download hogi (isko safe rakhna!)

### Step 8: GitHub Secrets add karo
1. GitHub pe apne repo pe jao
2. **Settings** → **Secrets and variables** → **Actions**
3. **"New repository secret"** click karo, 2 secrets banao:

**Secret 1:**
- Name: `FIREBASE_SERVICE_ACCOUNT`
- Value: Download ki hui JSON file ka **poora content** paste karo

**Secret 2:**
- Name: `FIREBASE_PROJECT_ID`
- Value: Apna Firebase project ID (e.g. `guitarmate-xxxxx`)

4. Save karo

---

## PART 4 — Firebase Hosting Connect karo

### Step 9: Firebase CLI se deploy karo (pehli baar)
```bash
# Firebase CLI install karo (ek baar)
npm install -g firebase-tools

# Login karo
firebase login

# Project link karo
firebase use --add
# → apna project select karo

# Pehli baar manually deploy karo
firebase deploy --only hosting
```

Terminal mein ek URL milega:
```
Hosting URL: https://guitarmate-xxxxx.web.app
```

**Yahi tumhara live app URL hai! 🎉**

---

## PART 5 — Auto Deploy Test karo

### Step 10: Auto deploy verify karo
Ab jab bhi `main` branch pe code push karo:
```bash
git add .
git commit -m "Update app"
git push origin main
```

GitHub Actions automatically Firebase pe deploy kar dega!

GitHub → Actions tab mein deploy ka progress dekh sakte ho.

---

## Final Checklist ✅

- [ ] Firebase project create kiya
- [ ] Web app register kiya
- [ ] `firebase-config.js` mein apna config paste kiya
- [ ] Email/Password auth enable kiya
- [ ] Google auth enable kiya
- [ ] Firestore database banaya
- [ ] Firestore rules set kiye
- [ ] GitHub repo banaya
- [ ] Code push kiya
- [ ] Service account key download ki
- [ ] GitHub secrets set kiye (`FIREBASE_SERVICE_ACCOUNT` + `FIREBASE_PROJECT_ID`)
- [ ] `firebase deploy` se pehli baar deploy kiya
- [ ] Live URL pe app khuli ✅

---

## Test karo
1. Live URL pe jao
2. **Sign Up** se naya account banao
3. Ek song add karo
4. **Dusre phone ya browser** mein same account se login karo
5. Song wahan bhi dikhna chahiye ✅

---

## Troubleshooting

**"Permission denied" Firestore error?**
→ Firestore Rules dobara check karo, Publish kiya ya nahi

**Google Sign-in kaam nahi kar raha?**
→ Firebase Console → Authentication → Google provider → "Authorized domains" mein apna domain add karo

**GitHub Actions fail ho raha?**
→ Secrets sahi set hue hain? JSON me koi extra space/character toh nahi?

**App khul nahi rahi?**
→ `firebase-config.js` mein config sahi paste hua?
