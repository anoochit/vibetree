# Product Requirement Document (PRD): "VibeTree" with Firebase & Next.js

This document outlines the Product Requirements and step-by-step task execution roadmap for **VibeTree**—the real-world Linktree clone equipped with **Next.js (App Router), Tailwind CSS, Firebase Authentication, Firebase Firestore / Storage**, and ready for **Firebase App Hosting**.

The code for the project will be created and maintained in the **`vibetree`** subdirectory.

---

## 📋 Part 1: Product Requirement Document (PRD)

### 1. Executive Summary

**VibeTree** is a web-based, cloud-powered landing page builder. All user sign-up, login, and profile access control are managed via **Firebase Authentication** (supporting Email/Password and Google Sign-In). The application allows users to build, customize, and preview a highly personalized landing page of links inside a beautiful, live-updating mobile phone preview simulator. All profile states, links, and styling themes are stored in Firestore, customized files are uploaded to Firebase Storage, and secure access is protected by Firebase Authentication.

### 2. Feature Specifications

| Feature ID | Feature Name | Description | Priority |
| :--- | :--- | :--- | :--- |
| **F-01** | **Split-Screen Builder & Preview** | Left panel hosts the control builder; right panel displays a premium, glassmorphic floating mobile-phone container showcasing the live bio page. | P0 (Critical) |
| **F-02** | **Dynamic Link Management** | Create, rename, delete, and modify custom link items (Title + URL). Automatically detect social platform URLs (e.g., GitHub, YouTube, X/Twitter, Instagram, LinkedIn) to display premium brand icons. | P0 (Critical) |
| **F-03** | **Firebase Authentication & Multi-Tenancy** | Secure login/signup using **Firebase Authentication**. Each authenticated user manages their own unique landing page. Clean routing via **Next.js App Router**: `/` (home/auth landing page), `/dashboard` (editing), and `/:username` (public profile). | P0 (Critical) |
| **F-04** | **Firestore Synchronization & Firebase Setup Check** | Persist user profiles, configurations, and link arrays in Firestore. If Firebase configuration environment variables are missing, display a stunning, step-by-step setup helper page that guides the developer on how to configure and set up their Firebase credentials. | P0 (Critical) |
| **F-05** | **Firebase Storage Uploads** | Allow users to upload custom avatar images. Store assets in Firebase Storage and display the download URL. | P1 (High) |
| **F-06** | **Drag-and-Drop Reordering** | Order links via a lightweight custom HTML5 drag-and-drop interface on the builder side, syncing immediately to the live preview and database. | P1 (High) |
| **F-07** | **Custom Premium Themes** | Support 4 distinct high-end interactive visual themes: Dark Glassmorphism, Cyberpunk / Neon, Minimalist Light / Soft Pastel, and Retro / Neo-Brutalism. | P1 (High) |
| **F-08** | **Analytics Dashboard** | Integrate dynamic click metrics tracking, incrementing counts on link visits and rendering interactive Chart.js charts. | P2 (Medium) |
| **F-09** | **Firebase App Hosting Deployment** | Deploy the app securely using Firebase CLI Hosting / App Hosting. | P1 (High) |

### 3. Premium Themes & Design System

To ensure a highly premium, aesthetic, and unified visual identity, VibeTree supports 4 distinct themes controlled via a `data-theme` attribute on the page root.

#### A. 🌌 Dark Glassmorphism (`theme: 'glassmorphism'`)

* **Aesthetic & Feel**: Ultra-modern, high-end, macOS/iOS style frosted-glass overlays.
* **Colors**:
  * Background: Deep Slate/Obsidian (`#030712` to `#0b0f19` gradient)
  * Card Background: Semi-transparent white (`rgba(255, 255, 255, 0.07)`)
  * Border: Subtle transparent white (`rgba(255, 255, 255, 0.1)`)
  * Text: Crisp White (`#ffffff`) & Muted Gray (`#9ca3af`)
  * Accent Glow: Cyan (`#22d3ee`) & Violet (`#a78bfa`)
* **Typography**: Clean, professional geometric sans-serif: **Inter** or **Outfit**.
* **Micro-interactions / Animations**:
  * Cards feature a `backdrop-filter: blur(12px)`.
  * Hovering over link cards increases the backdrop blur to `20px`, expands a subtle soft cyan shadow glow, and scales the item up by $1.02$ with a smooth transition.

#### B. ⚡ Cyberpunk / Neon (`theme: 'cyberpunk'`)

* **Aesthetic & Feel**: Retro-futuristic, high-contrast, glowing cyberpunk terminal.
* **Colors**:
  * Background: Jet Black (`#09090b`) with a faint neon grid overlay
  * Card Background: Pure Black (`#000000`)
  * Border: High-intensity Electric Pink (`#ff007f`) or Cyber Cyan (`#00f0ff`)
  * Text: Bright Neon White (`#ffffff`) with pink/cyan text-shadow glow
* **Typography**: Sharp monospace/industrial: **JetBrains Mono** or **Space Grotesk**.
* **Micro-interactions / Animations**:
  * Hovering triggers a custom CSS text-flicker (simulating neon hum).
  * Borders transition from neon pink to neon cyan with a 3D box-shadow glow shift.
  * Cards scale up slightly ($1.01$) and skew $-1\deg$ dynamically.

#### C. 🌸 Minimalist Light / Soft Pastel (`theme: 'pastel'`)

* **Aesthetic & Feel**: Warm, calm, modern organic lifestyle brand.
* **Colors**:
  * Background: Warm Cream (`#faf8f5`)
  * Card Background: Matte White (`#ffffff`)
  * Border: Gentle Light Sage (`#e2e8f0`)
  * Text: Charcoal/Earthy Brown (`#1c1917`) & Muted Sage/Lavender
  * Accent: Soft Mint/Sage Green (`#a7f3d0`) or Lilac (`#f5d0fe`)
* **Typography**: Elegant serif headings combined with clean sans-serif bodies: **Playfair Display** + **Plus Jakarta Sans**.
* **Micro-interactions / Animations**:
  * Hovering triggers a very soft background color fade to a delicate pastel tint.
  * Cards lift off the page with a gentle vertical translation ($-2px$) and a highly diffused, warm shadow expansion.

#### D. 🧱 Retro / Neo-Brutalism (`theme: 'brutalism'`)

* **Aesthetic & Feel**: Playful, high-impact, zine-style raw web layout.
* **Colors**:
  * Background: Vibrant Banana Yellow (`#facc15`) or Bright Orange (`#f97316`)
  * Card Background: Crisp White (`#ffffff`) or Cyan (`#22d3ee`)
  * Border: Solid Extra-Thick Black (`4px Solid #000000`)
  * Text: Dark Charcoal/Black (`#000000`)
  * Accent: High-saturated primary colors
* **Typography**: Heavy, punchy, blocky display sans-serif: **Lexend Mega** or **Syne**.
* **Micro-interactions / Animations**:
  * Cards have a flat, hard, non-blurry offset shadow (`4px 4px 0px #000000`).
  * Hovering translates the card exactly $-4px$ up and $-4px$ left, while the hard shadow scales accordingly to simulate a physical clicking button or spring loaded tile.

---

## 🗄️ Part 1.5: Firestore Database Schema

To enable clean multi-tenancy, rapid username availability checks, and link analytics, the following Cloud Firestore schema is established.

### 1. `users` Collection

Stores the core profile, branding, and link arrays for each registered user.

* **Document ID**: `uid` (matching the user's Firebase Authentication ID)
* **Fields**:

  ```typescript
  {
    uid: string;                 // Firebase Auth UID
    username: string;            // Unique, lowercase profile handle (e.g., "john_doe")
    displayName: string;         // Display name shown on profile (e.g., "John Doe")
    bio: string;                 // Brief bio description
    avatarUrl: string;           // HTTPS URL pointing to custom avatar image (Firestore or Storage)
    theme: string;               // Active visual theme: 'glassmorphism' | 'cyberpunk' | 'pastel' | 'brutalism'
    links: Array<{
      id: string;                // Unique link ID (UUID format)
      title: string;             // Display text of the link
      url: string;               // Destination URL
      clicks: number;            // Total link clicks counter
    }>;
    createdAt: timestamp;        // ISO / Firebase Timestamp of registration
    updatedAt: timestamp;        // ISO / Firebase Timestamp of last modification
  }
  ```

### 2. `usernames` Collection

A secondary reverse-lookup collection designed to enforce username uniqueness with high performance, preventing race conditions.

* **Document ID**: `username` (the lowercase username, e.g., "john_doe")
* **Fields**:

  ```typescript
  {
    uid: string;                 // Pointer to the user's auth UID in the 'users' collection
    reservedAt: timestamp;       // Timestamp when this username was claimed
  }
  ```

### 3. `analytics` Subcollection

To drive Chart.js widgets without bloating the main profile document, historical click analytics are stored in a nested subcollection.

* **Collection Path**: `users/{uid}/analytics/{linkId}`
* **Document ID**: `linkId` (the matching unique link ID from the user's link array)
* **Fields**:

  ```typescript
  {
    linkId: string;              // Matches parent link ID
    clicksByDay: Array<{
      date: string;              // YYYY-MM-DD date format
      count: number;             // Count of clicks recorded on that date
    }>;
  }
  ```

---

## 🛡️ Part 1.7: Production-Grade Specifications

To ensure enterprise-grade security, discoverability, speed, and UX integrity, the following specifications are enforced:

### 1. Firestore & Storage Security Rules

#### Cloud Firestore Security Rules

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // 1. Users collection rules
    match /users/{uid} {
      allow read: if true; // Publicly readable profiles
      allow write: if request.auth != null && request.auth.uid == uid;
    }
    
    // 2. Usernames collection rules (enforces unique handles)
    match /usernames/{username} {
      allow read: if true; // Public check for availability
      allow create: if request.auth != null 
                    && request.resource.data.uid == request.auth.uid;
      allow delete, update: if request.auth != null 
                            && resource.data.uid == request.auth.uid;
    }
    
    // 3. Analytics subcollection rules
    match /users/{uid}/analytics/{linkId} {
      allow read: if request.auth != null && request.auth.uid == uid;
      allow write, update: if true; // Allow incrementing clicks publicly
    }
  }
}
```

#### Firebase Storage Security Rules

```javascript
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /avatars/{uid}/{allPaths=**} {
      allow read: if true; // Avatars are public
      allow write: if request.auth != null 
                    && request.auth.uid == uid
                    && request.resource.size < 2 * 1024 * 1024 // < 2MB limit
                    && request.resource.contentType.matches('image/.*'); // Image only
    }
  }
}
```

### 2. Dynamic SEO & OpenGraph Meta Tags

The public-facing profile page (`/[username]`) must dynamically compute metadata server-side using the Next.js `generateMetadata` API to maximize SEO and card previews on X, Discord, and Slack:

* **Browser Title**: `${displayName} (@${username}) | VibeTree`
* **Meta Description**: `${bio}` (falls back to: `"Check out ${displayName}'s links, social profiles, and content on VibeTree."` if bio is blank).
* **OpenGraph Schema**:
  * `og:title`: `${displayName} (@${username})`
  * `og:description`: `${bio}`
  * `og:image`: Uses the user's `avatarUrl` (or a fallback default aesthetic OpenGraph image).
  * `og:type`: `"profile"`

### 3. Avatar Upload & Image Optimization

* **Allowed Formats**: `.png`, `.jpg`, `.jpeg`, `.webp` (MIME checked client-side and server-side).
* **Upload Processing**: Before uploading to Firebase Storage, files should check for a maximum size of `2MB` to prevent storage bloat.
* **Next.js Optimization**: All avatars on public profiles and dashboards must be rendered using the Next.js `<Image />` component with fixed heights/widths, modern aspect ratios, WebP automatic translation, and soft blurred-edge preview styling to prevent layout shifts (CLS).

### 4. Link Validation Rules & UX Error Handling

* **Username Constraints**: Must be between 3 and 15 characters, containing only lowercase letters, numbers, and underscores (`/^[a-z0-9_]{3,15}$/`). Show real-time feedback on input.
* **Link URL Validation**: Must match a valid URL pattern starting with `http://` or `https://` (`/^(https?:\/\/)?([\da-z\.-]+)\.([a-z\.]{2,6})([\/\w \.-]*)*\/?$/`). Display a warning if the user enters a URL missing `https://` and auto-correct it.
* **Link Title Constraints**: Non-empty, maximum of `50` characters.
* **Profile Bio Constraints**: Maximum of `160` characters (standard micro-blogging length).
* **Interaction feedback**: Disable the dashboard save button during active Firestore sync or active file uploading, rendering smooth glowing spin-loader states to elevate premium UX.

---

## 🛠️ Part 2: Task Breakdown & Step-by-Step Implementation Roadmap

### 🟩 Milestone 1: Environment Setup, Routing & Auth Setup (P0)

* [ ] **Task 1.1:** Initialize Next.js (App Router) app in the **`vibetree`** directory with Tailwind CSS, `lucide-react`, and `chart.js`.
* [ ] **Task 1.2:** Configure tailwind setup, primary styles, and core CSS variables supporting the 4 dynamic themes.
* [ ] **Task 1.3:** Setup routing structure (`/`, `/dashboard`, `/[username]`) and configure Firebase SDK with an elegant environment-variable setup check (directing to a setup helper screen if config keys are missing).
* [ ] **Task 1.4:** Create stunning landing page with **Firebase Authentication** Email & Password/Google Authentication UI.
* [ ] **Task 1.5 [DoD / Verification]:** Confirm that visiting the site without `.env` variables successfully intercepts navigation and displays the setup helper screen. Verify that providing valid keys allows full authentication flows to run.

### 🟦 Milestone 2: Split-Panel Builder & Live Preview (P0)

* [ ] **Task 2.1:** Design split-panel dashboard (Left: control panel with forms; Right: glassmorphic floating mobile-phone frame).
* [ ] **Task 2.2:** Build Profile editor (Avatar upload, bio text, username, theme selector) with instant live preview.
* [ ] **Task 2.3:** Build Dynamic Link List builder (add, edit title, edit URL, delete links) with real-time reactive updating.
* [ ] **Task 2.4:** Implement automatic social URL recognition to auto-assign premium brand icons.
* [ ] **Task 2.5 [DoD / Verification]:** Verify that adding, updating, or deleting links and profile fields instantly updates the visual representation in the mock phone frame in real-time. Test multiple social links (YouTube, X, GitHub) and verify icons appear correctly.

### 🔥 Milestone 3: Firestore Synchronization & Real-Time Sync (P0)

* [ ] **Task 3.1:** Map profile state and link edits to Firestore `.set()` or `.update()` actions using the `users` and `usernames` schema.
* [ ] **Task 3.2:** Implement real-time listener updates using `onSnapshot` for dashboard editing.
* [ ] **Task 3.3:** Design and implement the public profile page (`/[username]`) which loads user profiles from Firestore in real-time.
* [ ] **Task 3.4 [DoD / Verification]:** Open two parallel browser windows (Dashboard & Public Profile). Edit links in the dashboard and verify that changes propagate to the public profile instantly without page reload. Test duplicate usernames and verify uniqueness protection.

### 📷 Milestone 4: Storage Uploads & Custom Drag-and-Drop (P1)

* [ ] **Task 4.1:** Embed custom avatar file input. Upload chosen image to Firebase Storage and save the returned download URL to Firestore.
* [ ] **Task 4.2:** Implement standard HTML5 draggable elements on link rows in the builder panel.
* [ ] **Task 4.3:** Rearrange the list array on dropping a link, and sync the updated ordering directly to Firestore.
* [ ] **Task 4.4 [DoD / Verification]:** Confirm drag-and-drop handles are responsive and reorder state immediately. Test uploading files larger than 2MB or non-image formats, checking that client-side validations block the upload and render an aesthetic warning toast.

### 🎨 Milestone 5: Styling Themes, Click Analytics & Deployment (P1/P2)

* [ ] **Task 5.1:** Integrate the 4 gorgeous themes using custom tailwind / CSS variables.
* [ ] **Task 5.2:** Add click metric event tracking. Increment Firestore metric counters when preview links are clicked.
* [ ] **Task 5.3:** Create interactive dashboard charts tracking click performance using Chart.js.
* [ ] **Task 5.4:** Prepare deployment configurations and run `firebase deploy`.
* [ ] **Task 5.4b:** Create local `firestore.rules` and `storage.rules` files matching our security specifications in Part 1.7, and configure `firebase.json` to deploy these rules.
* [ ] **Task 5.5 [DoD / Verification]:** Click on active public links, then verify click counts increment correctly inside Firestore subcollections. Check that the Chart.js widgets render these clicks accurately over a timeline. Verify the fully deployed app renders correctly on mobile devices.
