# WebSocket Connection Fix Guide

## Problem
WebSocket connection errors with malformed URL: `wss://%20https/socket.io/`

This indicates the `VITE_API_URL` environment variable has extra spaces or incorrect formatting.

---

## ✅ Fix for Vercel (Frontend)

### Step 1: Update Environment Variable

1. Go to [Vercel Dashboard](https://vercel.com/dashboard)
2. Select your project
3. Navigate to **Settings** → **Environment Variables**
4. Find `VITE_API_URL` and click **Edit** (or delete and recreate)

### Step 2: Set Correct Value

**CRITICAL:** The value must be formatted EXACTLY like this:

```
https://your-backend-app.onrender.com
```

**❌ WRONG Examples:**
- ` https://your-backend-app.onrender.com` (space before)
- `https://your-backend-app.onrender.com ` (space after)
- `https://your-backend-app.onrender.com/` (trailing slash)
- `"https://your-backend-app.onrender.com"` (quotes)
- `http://your-backend-app.onrender.com` (http instead of https)

**✅ CORRECT Example:**
```
https://akhilcollab-backend.onrender.com
```

### Step 3: Apply to All Environments

Make sure to set this for:
- **Production**
- **Preview**
- **Development** (optional)

### Step 4: Redeploy

1. Go to **Deployments** tab
2. Click the **︙** (three dots) on the latest deployment
3. Select **Redeploy**
4. Wait for deployment to complete

---

## ✅ Fix for Render (Backend)

### Step 1: Verify Environment Variables

1. Go to [Render Dashboard](https://render.com/dashboard)
2. Select your backend service
3. Navigate to **Environment** tab
4. Verify these variables exist:

```bash
NODE_ENV=production
MONGODB_URI=your_mongodb_connection_string
FRONTEND_URL=https://your-frontend-app.vercel.app
PORT=5000
```

**IMPORTANT:** `FRONTEND_URL` must be your exact Vercel URL (without trailing slash)

### Step 2: Check Service Settings

1. Ensure **Plan Type** is at least "Free" (not suspended)
2. Check **Health Check Path**: `/` or leave empty
3. Verify service is **Active** (green indicator)

### Step 3: Restart Service (if needed)

1. Click **Manual Deploy** → **Deploy latest commit**
2. Or click **Suspend** then **Resume** to force restart

---

## 🧪 Testing

After deploying both services:

### 1. Test Backend Health
Open in browser:
```
https://your-backend-app.onrender.com/
```
Should return: "Server is running"

### 2. Test WebSocket Connection
1. Open your Vercel app in browser
2. Open Browser DevTools (F12)
3. Go to **Console** tab
4. Look for:
   - ✅ `Socket connected successfully`
   - ❌ No more `WebSocket connection to 'wss://%20https/socket.io/' failed`

### 3. Test Socket Features
- Try logging in
- Navigate to Dashboard
- Open a card workspace
- Check Messages
- Verify real-time updates work

---

## 📋 Common Issues & Solutions

### Issue 1: Still seeing `wss://%20https/` error
**Solution:** Environment variable wasn't updated properly
- Delete the variable completely in Vercel
- Create a new one with correct value
- Redeploy

### Issue 2: Connection to `ws://localhost:5000`
**Solution:** Browser is caching old environment variables
- Hard refresh: `Ctrl + Shift + R` (Windows) or `Cmd + Shift + R` (Mac)
- Or clear browser cache

### Issue 3: CORS errors
**Solution:** Update `FRONTEND_URL` in Render
```bash
# In Render Environment Variables
FRONTEND_URL=https://your-exact-vercel-url.vercel.app
```
Then redeploy backend

### Issue 4: WebSocket connects but disconnects immediately
**Solution:** Check Render logs
1. Go to Render Dashboard → Your Service
2. Click **Logs** tab
3. Look for connection errors
4. Ensure MongoDB is connected

---

## 🔍 Debugging Commands

### Check current environment in deployed app:
Open browser console on your Vercel app and run:
```javascript
console.log(import.meta.env.VITE_API_URL)
```
Should output: Your Render backend URL (not localhost)

### Check WebSocket status:
```javascript
// In browser console on your Vercel app
window.socketStatus = () => {
  console.log('Socket connected:', window.socket?.connected)
  console.log('Socket URL:', window.socket?.io?.uri)
}
socketStatus()
```

---

## 📝 Checklist

Before considering this fixed, verify:

- [ ] `VITE_API_URL` in Vercel has NO spaces, NO quotes, NO trailing slash
- [ ] `VITE_API_URL` starts with `https://` (not `http://`)
- [ ] `FRONTEND_URL` in Render matches your Vercel URL exactly
- [ ] Vercel app redeployed after environment variable change
- [ ] Backend service is active on Render
- [ ] Console shows "Socket connected successfully"
- [ ] No more WebSocket errors in browser console
- [ ] Real-time features work (messages, card updates, etc.)

---

## 🆘 Still Not Working?

If issues persist:

1. **Share the following info:**
   - Your Vercel app URL
   - Your Render backend URL
   - Screenshot of browser console errors
   - Screenshot of Vercel environment variables (hide sensitive values)

2. **Verify connectivity:**
   ```bash
   # Test if backend is accessible
   curl https://your-backend-app.onrender.com/
   ```

3. **Check Render service status:**
   - Ensure service isn't sleeping (Free tier sleeps after 15 min of inactivity)
   - First request may take 30-60 seconds to wake up

---

Good luck! 🚀
