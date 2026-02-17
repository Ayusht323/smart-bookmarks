# Smart Bookmarks App

A robust, real-time bookmark manager built with **Next.js (App Router)** and **Supabase**.

🔗 **Live Demo:** https://smart-bookmarks-taupe.vercel.app/

---

## 🚀 Features

* **Google Authentication:** Secure sign-up and login without passwords.
* **Real-time Sync:** Bookmarks appear instantly across multiple tabs/devices.
* **Optimistic UI:** Instant feedback for adding/deleting items (zero latency feel).
* **Row Level Security (RLS):** Data is strictly private. User A cannot access User B's bookmarks.
* **Responsive Design:** Styled with Tailwind CSS for mobile and desktop.

---

## 🛠️ Tech Stack

* **Framework:** Next.js 14 (App Router)
* **Database & Auth:** Supabase (PostgreSQL + GoTrue)
* **Styling:** Tailwind CSS
* **Deployment:** Vercel

---

## 🧠 Challenges & Solutions

During development, I encountered several interesting challenges. Here is how I solved them:

### 1. Robust Real-time Updates (The "Hybrid" Approach)
**The Problem:**
While implementing Supabase Realtime (WebSockets), I noticed that certain strict network environments (like corporate firewalls or aggressive browser privacy settings) can block WebSocket (`wss://`) connections. This caused the "live sync" feature to fail for some users, degrading the experience.

**The Solution:**
I implemented a **Hybrid Sync Strategy**:
1.  **Primary:** The app establishes a WebSocket connection via Supabase Channels for immediate, event-driven updates.
2.  **Fallback:** I added a "Short Polling" mechanism that silently checks for divergence every few seconds.

**Result:** The app is now "network agnostic." If WebSockets work, updates are instant (0ms). If they are blocked, the app self-heals within seconds, guaranteeing the user always sees the latest data without needing to refresh.

### 2. Optimistic UI for Better UX
**The Problem:**
Initially, when adding a bookmark, the app waited for the round-trip database confirmation before updating the list. This introduced a noticeable delay (latency) that made the app feel sluggish.

**The Solution:**
I implemented **Optimistic Updates**. When a user clicks "Add" or "Delete":
1.  The UI updates **immediately** (using a temporary ID).
2.  The network request is sent in the background.
3.  If the request fails, the UI rolls back; otherwise, it silently swaps the temporary ID for the real database ID.

### 3. Preventing Duplicate Events
**The Problem:**
Because I combined *Optimistic Updates* (manual local add) with *Real-time Listeners* (server push), users would briefly see the same bookmark twice: once from their click, and milliseconds later from the server event.

**The Solution:**
I added a deduplication check in the Realtime subscription logic. The app checks if the incoming bookmark ID already exists in the local state before appending it, ensuring a clean, flicker-free list.

---

## 🏃‍♂️ How to Run Locally

1.  **Clone the repo:**
    ```bash
    git clone [https://github.com/YOUR_USERNAME/smart-bookmarks.git](https://github.com/YOUR_USERNAME/smart-bookmarks.git)
    cd smart-bookmarks
    ```

2.  **Install dependencies:**
    ```bash
    npm install
    ```

3.  **Set up Environment Variables:**
    Create a `.env.local` file:
    ```env
    NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
    NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
    ```

4.  **Run the server:**
    ```bash
    npm run dev
    ```
