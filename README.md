# 7v7 Game Time Tracker

A web app for tracking playing time, subs and formations for a 7v7 soccer team.
Teammates open the link and see the squad, live game and history (no account needed).
Only the coach can make changes, after signing in with Google.

It runs on free GitHub Pages and a free Firebase database. There's no server to maintain.

## One-time Firebase setup (about 15 minutes)

1. **Create a project.** Go to <https://console.firebase.google.com>, click **Create a project**,
   name it (e.g. `gametime`), and turn Google Analytics **off**.

2. **Add a database.** In the left menu go to **Build → Firestore Database → Create database**.
   Pick a location near you (e.g. `northamerica-northeast1` for Montreal/Toronto), and start in **production mode**.

3. **Set who can edit.** In Firestore open the **Rules** tab and replace everything with the contents of
   [`firestore.rules`](firestore.rules). Change `COACH_EMAIL` to the Gmail address you'll sign in with, then **Publish**.

4. **Turn on Google sign-in.** Go to **Build → Authentication → Get started → Sign-in method → Google**,
   switch it **on**, choose your support email, and **Save**.
   Then open **Authentication → Settings → Authorized domains → Add domain**, and add `YOUR-GITHUB-NAME.github.io`.

5. **Get the app config.** Open **Project settings** (gear icon) → **Your apps** → click the **`</>`** (Web) icon.
   Name it `gametime`, leave Hosting unticked, and click **Register app**. Copy the `firebaseConfig = { ... }` object it shows.

6. **Paste it into `config.js`** in this repo (on GitHub: open the file → pencil icon → edit → **Commit changes**):

   ```js
   window.FIREBASE_CONFIG = {
     apiKey: "...",
     authDomain: "...",
     projectId: "...",
     storageBucket: "...",
     messagingSenderId: "...",
     appId: "..."
   };
   ```

   These values are fine to publish: the rules in step 3 control who can change data.

7. Wait a minute for GitHub Pages to update, then open the site.

## Move your data in

1. In the old Claude tracker go to **History → Export → Backup → Copy**.
2. On the new site tap **Coach sign in** and sign in with the Gmail from step 3.
   The status line should say **Saved · live for the team**.
3. Go to **History → Restore**, paste the backup, and tap **Restore**.

## Status line

| Status | Meaning |
| --- | --- |
| Live · view only | Teammate view: updates live, nothing can be changed |
| Saved · live for the team | Coach is signed in and changes are saved |
| Saving… | Sending a change |
| Offline · will sync when back online | No signal. Changes are kept on the phone and sent later |
| This Google account can't edit | Signed in with an account that isn't `COACH_EMAIL` in the rules |
| Not connected to Firebase yet | `config.js` hasn't been filled in. The app works on this device only |

## Adding more coaches

In the Firestore rules, change the email line to allow a list:

```
&& request.auth.token.email in ["you@gmail.com", "assistant@gmail.com"];
```
