# Setting Up gdrive CLI for Automated Google Drive Access

This guide will help you set up gdrive credentials for automated file uploads, downloads, and management.

## 🎯 What You'll Get

After setup, you'll be able to:
- ✅ Upload large files (>10MB) to Google Drive programmatically
- ✅ Convert PPTX → Google Slides automatically
- ✅ Clone, copy, and manage Google Drive files via CLI
- ✅ Make programmatic edits to Google Slides (with API integration)

**Time to complete:** ~10 minutes

---

## 📋 Step-by-Step Setup

### Step 1: Access Google Cloud Console

1. Go to: [Google Cloud Console](https://console.cloud.google.com/)
2. Sign in with your Grafana Google account

### Step 2: Create a New Project (or use existing)

1. Click the project dropdown at the top
2. Click "**New Project**"
3. Name it: `gdrive-cli-automation` (or similar)
4. Click "**Create**"
5. Wait for project creation (~30 seconds)
6. Select your new project from the dropdown

### Step 3: Enable Google Drive API

1. In the search bar, type: `Google Drive API`
2. Click on "**Google Drive API**"
3. Click "**Enable**" button
4. Wait for API to enable

### Step 4: Configure OAuth Consent Screen

1. Go to: **APIs & Services** → **OAuth consent screen** (left sidebar)
2. Select "**External**" user type
3. Click "**Create**"

**Fill in the form:**
- **App name:** `gdrive CLI` (or your preferred name)
- **User support email:** Your email address
- **Developer contact email:** Your email address
- Leave other fields as optional
4. Click "**Save and Continue**"

### Step 5: Add OAuth Scopes

1. Click "**Add or Remove Scopes**"
2. In the filter box, search for: `drive`
3. Select these two scopes:
   - ✅ `.../auth/drive` (Full Drive access)
   - ✅ `.../auth/drive.metadata.readonly` (Read metadata)
4. Click "**Update**"
5. Click "**Save and Continue**"

### Step 6: Add Test Users (Important!)

1. Click "**Add Users**"
2. Add your email address (and any team members who will use this)
3. Click "**Add**"
4. Click "**Save and Continue**"

### Step 7: Publish the App

⚠️ **Critical:** If you don't publish, your tokens will expire after 7 days!

1. Go back to "**OAuth consent screen**"
2. Click "**Publish App**" button
3. Confirm by clicking "**Confirm**"

**Note:** For internal/personal use, you don't need Google verification

### Step 8: Create OAuth Credentials

1. Go to: **APIs & Services** → **Credentials** (left sidebar)
2. Click "**+ Create Credentials**" (top)
3. Select "**OAuth client ID**"
4. Application type: Select "**Desktop app**"
5. Name: `gdrive-cli-desktop` (or your preferred name)
6. Click "**Create**"

### Step 9: Save Your Credentials

A popup will appear with:
- **Client ID:** (long string starting with numbers)
- **Client Secret:** (shorter string)

**IMPORTANT:**
- ✅ Click "**Download JSON**" (recommended)
- ✅ OR copy both values to a secure location
- ⚠️ Keep these private (like a password)

---

## 🔐 Step 10: Authenticate gdrive

Now let's connect gdrive to your Google account.

### Run the authentication command:

```bash
gdrive account add
```

### You'll be prompted for:

1. **Client ID:** Paste the Client ID from Step 9
2. **Client Secret:** Paste the Client Secret from Step 9
3. **Browser authorization:**
   - A URL will appear in the terminal
   - Open it in your browser
   - Sign in with your Google account
   - Click "**Continue**" (ignore the "unverified app" warning for internal use)
   - Grant permissions
   - You'll see "Success! You may now close this tab"

### Verify authentication:

```bash
gdrive account list
```

You should see your account listed!

---

## ✅ Test Your Setup

Try listing your Google Drive files:

```bash
gdrive files list
```

Try uploading a test file:

```bash
gdrive files import /path/to/test.pptx
```

---

## 🚀 Next Steps: Automated Editing

Once gdrive is set up, we can:

1. **Upload large presentations:**
   ```bash
   gdrive files import presentation.pptx --print-only-id
   ```

2. **Clone presentations:**
   ```bash
   gdrive files copy <FILE_ID>
   ```

3. **Make programmatic edits** using Google Slides API (via Python)

---

## 🔒 Security Notes

- **Credentials are stored locally** in: `~/.gdrive/`
- **Never commit** credentials to git repositories
- **Revoke access anytime** at: https://myaccount.google.com/permissions
- For shared team access, consider creating a service account instead

---

## 📚 Additional Resources

- [gdrive GitHub](https://github.com/glotlabs/gdrive)
- [Official credential setup guide](https://github.com/glotlabs/gdrive/blob/main/docs/create_google_api_credentials.md)
- [Google Drive API documentation](https://developers.google.com/drive/api/guides/about-sdk)

---

## 🆘 Troubleshooting

**"Unverified app" warning when authorizing:**
- This is normal for personal projects
- Click "Advanced" → "Go to [app name] (unsafe)" → "Continue"
- For production, submit app for Google verification

**"Access blocked: This app's request is invalid":**
- Make sure you added your email as a test user (Step 6)
- Make sure you published the app (Step 7)

**Token expired after 7 days:**
- You forgot to publish the app (Step 7)
- Re-do Step 7 to publish

---

Ready to set this up? Let me know if you hit any issues during the process!
