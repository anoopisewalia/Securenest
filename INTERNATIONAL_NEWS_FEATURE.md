# International News Detection Feature

## ✅ Feature Implemented!

Your fake news detection system now supports **both National and International news** verification!

---

## 🌍 What's New

### **Two Verification Modes:**

1. **National News** (Default)
   - Requires country selection
   - Checks country-specific trusted sources
   - Example: Indian Railways news → checks NDTV, The Hindu, Times of India

2. **International News** (New!)
   - No country required
   - Checks international organizations and global news sources
   - Example: UN/WHO announcements → checks UN.org, WHO.int, BBC, Reuters

---

## 🎯 International Sources Added

### **International News Organizations:**
- BBC (0.96 reliability)
- Reuters (0.97)
- Associated Press (0.96)
- Al Jazeera (0.90)
- The Guardian (0.93)
- CNN International (0.88)
- France 24 (0.89)
- Deutsche Welle (0.91)
- Euronews (0.88)

### **International Organizations:**
- **United Nations** (un.org) - 0.98
- **World Health Organization** (who.int) - 0.98
- **UNESCO** (unesco.org) - 0.97
- **UNICEF** (unicef.org) - 0.97
- **World Bank** (worldbank.org) - 0.96
- **IMF** (imf.org) - 0.96
- **WTO** (wto.org) - 0.95
- **NATO** (nato.int) - 0.94
- **European Union** (europa.eu) - 0.95
- **International Red Cross** (icrc.org) - 0.96

### **Scientific Organizations:**
- **NASA** (nasa.gov) - 0.98
- **ESA** (esa.int) - 0.97
- **CERN** (cern.ch) - 0.97
- **Nature** (nature.com) - 0.97
- **Science Magazine** (science.org) - 0.97

---

## 📱 Frontend UI Changes

### **New Radio Button Selection:**

```
News Scope (required)
○ National News
○ International News (UN, WHO, BBC, etc.)
```

### **Country Field Behavior:**
- **National Mode:** Country is **required**
- **International Mode:** Country is **disabled** (grayed out)

---

## 🔍 How It Works

### **National News Example:**

**Input:**
```json
{
  "text": "Indian Railways announces new safety measures",
  "scope": "national",
  "country": "IN"
}
```

**Processing:**
- Searches Indian news sources (NDTV, The Hindu, etc.)
- Checks regional sources if state provided
- Verifies against country-specific trusted sources

**Result:** ✅ Real (if found in NDTV, The Hindu, etc.)

---

### **International News Example:**

**Input:**
```json
{
  "text": "WHO declares new global health emergency",
  "scope": "international"
}
```

**Processing:**
- Searches international sources (BBC, Reuters, UN, WHO)
- Checks official organization websites (who.int, un.org)
- Verifies against global trusted sources
- **No country required!**

**Result:** ✅ Real (if found on who.int, BBC, Reuters, etc.)

---

## 🧪 Test Cases

### Test 1: International News (Real)

**Input:**
- Scope: **International**
- Text: "UN Security Council passes resolution on climate change"
- Country: (leave empty)

**Expected:**
- Verdict: **Likely Real**
- Sources: UN.org, BBC, Reuters
- Confidence: 85%+

---

### Test 2: International News (Fake)

**Input:**
- Scope: **International**
- Text: "WHO confirms chocolate cures all diseases"
- Country: (leave empty)

**Expected:**
- Verdict: **Likely Fake**
- Reason: No trusted international sources found
- Confidence: 75%+

---

### Test 3: National News (Real)

**Input:**
- Scope: **National**
- Text: "Indian Railways announces new safety measures"
- Country: **IN**

**Expected:**
- Verdict: **Likely Real**
- Sources: NDTV, The Hindu, Times of India
- Confidence: 85%+

---

### Test 4: National News (Fake)

**Input:**
- Scope: **National**
- Text: "Taj Mahal to be painted gold next month"
- Country: **IN**

**Expected:**
- Verdict: **Likely Fake**
- Reason: Suspicious claim, no trusted sources
- Confidence: 75%+

---

## 🔧 API Changes

### **Request Format (Updated):**

```json
{
  "url": "https://example.com/article",  // Optional
  "text": "News text content",           // Optional
  "scope": "national" | "international", // NEW! Default: "national"
  "country": "IN",                       // Required for national, optional for international
  "state": "Punjab"                      // Optional
}
```

### **Validation Rules:**

1. **National Scope:**
   - `country` is **required**
   - `state` is optional
   - Checks country-specific sources

2. **International Scope:**
   - `country` is **optional** (ignored if provided)
   - `state` is ignored
   - Checks international sources only

---

## 📊 Detection Logic Changes

### **Source Priority (International Mode):**

1. **Fact-checkers** (highest priority - always checked)
2. **International organizations** (UN, WHO, NASA, etc.)
3. **International news** (BBC, Reuters, AP, etc.)
4. **Global sources** (fallback)

### **Source Priority (National Mode):**

1. **Fact-checkers** (highest priority - always checked)
2. **Country-specific sources** (NDTV, The Hindu for India)
3. **Regional sources** (if state provided)
4. **Global sources** (fallback)

---

## 🚀 How to Use

### **Step 1: Start all servers**

```bash
# Terminal 1: Python API
cd C:\Users\ASUS\Downloads\SecureNest\fakecheck-api
python -m uvicorn app.main:app --reload --port 8080

# Terminal 2: Node.js Backend
cd C:\Users\ASUS\Downloads\SecureNest\SecureNest\Chat-App\backend
npm run dev

# Terminal 3: React Frontend
cd C:\Users\ASUS\Downloads\SecureNest\SecureNest\Chat-App\frontend
npm run dev
```

### **Step 2: Open browser**

Go to: **http://localhost:5173**

### **Step 3: Navigate to Fake Check page**

### **Step 4: Test International News**

1. Select: **○ International News**
2. Enter text: "WHO announces new pandemic preparedness plan"
3. Leave country empty
4. Click **Check**

**Result:** Should verify against WHO.int, BBC, Reuters, etc.

### **Step 5: Test National News**

1. Select: **○ National News**
2. Enter text: "Indian Railways announces new safety measures"
3. Select country: **IN**
4. Click **Check**

**Result:** Should verify against NDTV, The Hindu, Times of India, etc.

---

## 📝 Files Modified

### **Backend (Python):**
- ✅ `app/main.py` - Added scope parameter, updated validation
- ✅ `app/trusted_sources.py` - Added INTERNATIONAL sources list
- ✅ `app/retrieval.py` - Updated search queries for international scope

### **Frontend (React):**
- ✅ `FakeCheckPage.jsx` - Added scope radio buttons, conditional country field

### **No changes needed:**
- ❌ Backend Node.js routes (automatically handles new field)
- ❌ Database schema (backward compatible)

---

## 🎉 Benefits

1. **✅ Flexible:** Handles both national and international news
2. **✅ Accurate:** Uses appropriate sources for each scope
3. **✅ User-friendly:** Simple radio button selection
4. **✅ Comprehensive:** 25+ international sources added
5. **✅ Reliable:** Highest reliability scores (0.95-0.98) for official organizations

---

## 🐛 Troubleshooting

### Issue: "Country is required" error for international news

**Solution:** Make sure you selected "International News" radio button. The country field should be disabled.

### Issue: No international sources found

**Solution:** 
1. Check if Python server is running
2. Verify internet connection
3. Check API keys in `.env` file

### Issue: Frontend not showing scope option

**Solution:** 
1. Restart React dev server
2. Clear browser cache
3. Check console for errors

---

## 🎯 Summary

Your fake news detection system now intelligently handles:

- **National news** → Country-specific verification
- **International news** → Global organization verification
- **Automatic source selection** based on scope
- **25+ international sources** including UN, WHO, NASA
- **User-friendly interface** with radio button selection

**Ready to test!** 🚀
