# Google OAuth Troubleshooting Guide

## ❌ Error: "invalid_client (Unauthorized)"

This error occurs when Google cannot verify your OAuth client credentials. Here's how to fix it:

### Step 1: Verify Client Secret Format

Your Google Client Secret should look like this:
```
GOCSPX-xxxxxxxxxxxxxxxxxxxxxxxx
```

**NOT** like:
- `Iamshakti@1221` ❌ (This is a password, not a Client Secret)
- Plain text passwords ❌
- Any other custom format ❌

### Step 2: Get the Correct Client Secret

1. **Go to Google Cloud Console:**
   https://console.cloud.google.com/apis/credentials

2. **Find Your OAuth 2.0 Client ID:**
   - Look for "Web client 1" or your client name
   - You should see your Client ID: `1020463452031-fekomab73gs54accfnemdpefkiqe6io4.apps.googleusercontent.com`

3. **View/Reset Client Secret:**
   - Click on your OAuth client
   - You'll see "Client Secret" section
   - If you can't see it, click "Reset Secret" to generate a new one
   - Copy the secret that starts with `GOCSPX-`

### Step 3: Update .env.local

Your `.env.local` should look like this:

```env
# Database
MONGODB_URI=mongodb+srv://shakti1221:5a%23igLmDWtcFsGn@cluster0.qpuewaa.mongodb.net/service-center?retryWrites=true&w=majority

# NextAuth Configuration
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=metacontroller-dev-secret-key-2024

# Google OAuth
GOOGLE_CLIENT_ID=1020463452031-fekomab73gs54accfnemdpefkiqe6io4.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=GOCSPX-your-actual-secret-here
```

### Step 4: Verify Redirect URIs

In Google Cloud Console, ensure these URIs are added:

**Authorized JavaScript origins:**
```
http://localhost:3000
```

**Authorized redirect URIs:**
```
http://localhost:3000/api/auth/callback/google
```

### Step 5: Restart Your Server

After updating `.env.local`:

```powershell
# Stop the server (Ctrl+C)
# Then restart:
npm run dev
```

## Common Issues

### Issue 1: Using Password as Client Secret
**Problem:** You're using `Iamshakti@1221` which is a password, not a Google OAuth client secret.

**Solution:** Get the actual Client Secret from Google Cloud Console (starts with `GOCSPX-`)

### Issue 2: Client Secret Not Set
**Problem:** Environment variable is empty or incorrect

**Solution:** 
```bash
# Check your .env.local file
cat .env.local

# Ensure GOOGLE_CLIENT_SECRET is set correctly
```

### Issue 3: Redirect URI Mismatch
**Problem:** Google redirects don't match configured URIs

**Solution:** Must be exactly:
- `http://localhost:3000/api/auth/callback/google` (with /api/auth/callback/google)
- No trailing slashes
- Correct protocol (http for localhost)

### Issue 4: OAuth Consent Screen Not Configured
**Problem:** OAuth consent screen is not set up

**Solution:**
1. Go to "OAuth consent screen" in Google Cloud Console
2. Fill in required fields (App name, support email, etc.)
3. Add test users (your email) if app is in testing mode
4. Save and continue

## Testing the Fix

1. **Clear browser cookies** (important!)
2. **Restart dev server**
3. **Visit:** http://localhost:3000/auth/signin
4. **Click "Continue with Google"**
5. **Should see Google sign-in page** (not an error)

## Expected .env.local Format

```env
MONGODB_URI=mongodb+srv://...
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=your-secret-key-here
GOOGLE_CLIENT_ID=1020463452031-fekomab73gs54accfnemdpefkiqe6io4.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=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from-console
```

## Still Not Working?

1. **Regenerate Client Secret:**
   - Go to Google Cloud Console
   - Click on your OAuth client
   - Click "Reset Secret"
   - Copy new secret
   - Update .env.local
   - Restart server

2. **Check Console Output:**
   - Look for the `clientSecret` in debug logs
   - Should start with `GOCSPX-`
   - If it shows your password, it's wrong

3. **Try Credential Login:**
   - Use email/password instead
   - Create account at: http://localhost:3000/auth/signup
   - Use any email with password: `demo123`

## Need the Actual Client Secret?

⚠️ **Security Warning:** Never share your Client Secret publicly!

To get it:
1. Open: https://console.cloud.google.com/apis/credentials
2. Click on "Web client 1"
3. Find "Client secret" field
4. Click "Show" or "Reset"
5. Copy the value starting with `GOCSPX-`
6. Paste in `.env.local`
7. Restart dev server

---

**After fixing, you should see:**
- Google sign-in popup opens
- You can select your Google account
- Returns to your app signed in
- No "invalid_client" errors
