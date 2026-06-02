# Quick Start Guide - SecureNest with Fake News Detection

## Start All Servers (3 Terminals Required)

### Terminal 1: Python API (FakeCheck Microservice)
```bash
cd C:\Users\ASUS\Downloads\SecureNest\fakecheck-api
python -m uvicorn app.main:app --reload --port 8080
```
**OR** double-click: `start_server.bat`

**Wait for:** `Application startup complete` message

---

### Terminal 2: Node.js Backend
```bash
cd C:\Users\ASUS\Downloads\SecureNest\SecureNest\Chat-App\backend
npm run dev
```

**Wait for:** `Server running on port 5003` message

---

### Terminal 3: React Frontend
```bash
cd C:\Users\ASUS\Downloads\SecureNest\SecureNest\Chat-App\frontend
npm run dev
```

**Wait for:** `Local: http://localhost:5173` message

---

## Access the Application

Open browser: **http://localhost:5173**

Navigate to **Fake Check** page and test with:

### Test Case 1: Real News (India)
- **Text:** "Indian Railways announces new safety measures for passengers"
- **Country:** IN
- **Expected:** Likely Real

### Test Case 2: Fake News Pattern
- **Text:** "Taj Mahal to be painted gold next month"
- **Country:** IN
- **Expected:** Likely Fake

### Test Case 3: URL Test
- **URL:** https://www.bbc.com/news/world
- **Country:** US
- **Expected:** Likely Real

---

## Troubleshooting

### Python API won't start
```bash
pip install -r requirements.txt
```

### Backend connection error
- Make sure Python API is running first (port 8080)
- Check `.env` has `FAKECHECK_API_URL=http://localhost:8080`

### Frontend not loading
- Check Node.js backend is running (port 5003)
- Check browser console for errors

---

## Server Status Check

✅ Python API: http://localhost:8080/health
✅ Node.js Backend: http://localhost:5003/api/auth/check
✅ React Frontend: http://localhost:5173

---

## Stop All Servers

Press `Ctrl + C` in each terminal window
