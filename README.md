# 🌙 Moon Light Cafe — Web App

Digital Menu & Order System — Customer ले मेनुबाट order गर्ने, Counter/Kitchen ले live dashboard मा हेरेर Pending → Accepted → Cooking → Done सम्म track गर्ने।

---

## 📁 के के छ यो प्रोजेक्टमा

```
moonlight-web/
├── public/
│   └── logo.jpg / logo.png        ← App को logo
├── src/
│   ├── App.jsx                    ← पूरा app (Customer + Counter दुवै मोड)
│   ├── firebase.js                ← Firebase config (तपाईंले भर्नुपर्ने ठाउँ)
│   ├── main.jsx                   ← Entry point
│   └── index.css
├── index.html
├── package.json
└── vite.config.js
```

यो **React + Vite** मा बनेको हो, र data **Firebase Firestore** मा real-time save हुन्छ — मतलब Customer ले order गर्ने बित्तिकै Counter Dashboard मा तुरुन्तै देखिन्छ, कुनै refresh नचाहिने।

---

## 🔧 Step 1 — Firebase Setup ✅ (पहिले नै भरिएको छ)

राम्रो खबर: तपाईंको Firebase project (`moon-light-cafe`) को config `src/firebase.js` मा पहिले नै भरिसकेको छ। तपाईंले अब यति मात्र confirm गर्नुपर्छ:

1. https://console.firebase.google.com मा जानुहोस् → `moon-light-cafe` project खोल्नुहोस्।
2. Left menu बाट **Build → Firestore Database** मा जानुहोस्।
3. यदि अहिलेसम्म **Firestore Database create गर्नुभएको छैन** भने, **Create database** क्लिक गर्नुहोस् → **Start in test mode** → आफ्नो region छान्नुहोस् (जस्तै `asia-south1`) → Enable गर्नुहोस्।
   - ⚠️ ध्यान दिनुहोस्: तपाईंको config मा `databaseURL` (Realtime Database) पनि देखिन्छ — तर यो app ले **Cloud Firestore** प्रयोग गर्छ (फरक service हो)। Firestore नै enable गर्नुपर्छ, Realtime Database होइन।
4. Test मोडमा database ७ दिनसम्म खुला रहन्छ, त्यसपछि बन्द हुन्छ — त्यसैले deploy गर्नुअघि Step 5 (Security Rules) पूरा गर्नुहोस्।

> 💡 तपाईंले Android app को लागि अलग Firebase config पनि दिनुभएको थियो (`messagingSenderId: 67721943429`), जो Web app को config (`127999911277`) भन्दा फरक देखियो। यदि दुवै एउटै Firebase project भित्र हुनुपर्ने हो भने, Firebase Console मा गएर दुवै app एउटै project भित्र छ कि छैन जाँच गर्नुहोस् (Project Settings → General → स्क्रोल तल "Your apps" मा दुवै देखिनुपर्छ)। अहिलेको लागि Web app ले आफ्नै Firestore project (`moon-light-cafe`) मा डाटा राख्छ, जो ठीकै छ — पछि Android app पनि बनाउनुपर्‍यो भने दुवैलाई उही project मा जोड्न सकिन्छ।

---

## 🔧 Step 2 — स्थानीय (Local) मा चलाउने

```bash
npm install
npm run dev
```

ब्राउजरमा देखिने link (जस्तै `http://localhost:5173`) खोल्नुहोस्। यदि Firebase config सही राखेको छ भने App ले काम गर्न थाल्छ।

---

## 🚀 Step 3 — GitHub मा राख्ने

```bash
git init
git add .
git commit -m "Moon Light Cafe web app"
git branch -M main
git remote add origin https://github.com/<तपाईंको-username>/moonlight-cafe.git
git push -u origin main
```

> `.gitignore` मा `node_modules` र `.env` पहिले नै छुटाइएको छ, त्यसैले ती push हुँदैनन्।

⚠️ **ध्यान दिनुहोस्**: `src/firebase.js` मा भएको config GitHub मा public देखिन्छ (यदि repo public छ भने)। यो ठीकै हो किनभने Firebase को web API key गोप्य राख्नु पर्दैन — तर Firestore Security Rules (तल हेर्नुहोस्) सेट नगरेसम्म जो कोहीले पनि तपाईंको database पढ्न/लेख्न सक्छ। त्यसैले Step 5 नछुटाउनुहोस्।

---

## 🌐 Step 4 — Internet मा Deploy गर्ने (Free)

### Option A: Vercel (सबैभन्दा सजिलो)
1. https://vercel.com मा GitHub ले login गर्नुहोस्।
2. **Add New → Project** → आफ्नो `moonlight-cafe` repo छान्नुहोस्।
3. Framework auto-detect हुन्छ (Vite) → **Deploy** थिच्नुहोस्।
4. २ मिनेटमा एउटा live URL पाउनुहुन्छ (जस्तै `moonlight-cafe.vercel.app`)।

### Option B: GitHub Pages (पूर्ण रूपमा free, GitHub भित्रै)
1. `package.json` मा यो थप्नुहोस्:
   ```json
   "scripts": {
     "deploy": "vite build && npx gh-pages -d dist"
   }
   ```
2. `npm install gh-pages --save-dev`
3. `vite.config.js` मा `base: "/moonlight-cafe/"` राख्नुहोस् (repo नाम अनुसार)।
4. `npm run deploy` चलाउनुहोस्।
5. GitHub repo → Settings → Pages मा गएर `gh-pages` branch छान्नुहोस्।

### Option C: Netlify
GitHub repo connect गर्ने, build command `npm run build`, publish directory `dist` राख्ने — त्यत्ति हो।

---

## 🔒 Step 5 — Firestore Security Rules (production अघि अनिवार्य)

Firebase Console → Firestore Database → **Rules** tab मा गएर यसो राख्नुहोस्:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /orders/{orderId} {
      allow read: if true;
      allow create: if request.resource.data.keys().hasAll(['table','items','total','stage'])
                    && request.resource.data.stage == 'Pending';
      allow update: if request.resource.data.diff(resource.data).affectedKeys()
                    .hasOnly(['stage']);
      allow delete: if false;
    }
  }
}
```

यसले: जोकोहीले मेनु हेर्न र order राख्न सक्छ (customer mode को लागि चाहिने), तर existing order को item/price जथाभावी edit गर्न सक्दैन — counter dashboard ले मात्र `stage` field बदल्न सक्छ। यो basic café-scale सुरक्षा हो; ठूलो business भएमा Firebase Authentication थप्न सुझाव दिन्छु (counter dashboard मा login राखेर)।

---

## ✨ Features

- **Customer Mode**: Search (Nepali/English दुवै), category filter, cart, table number, bill preview
- **Counter Dashboard**: Live order feed, Pending/Accepted/Cooking/Done tabs, एक क्लिकमा stage advance गर्ने, moon-phase visual progress
- **Real-time sync**: Firebase Firestore ले दुई मोडलाई जोड्छ — कुनै manual refresh चाहिँदैन
- **Bilingual UI**: Nepali + English content सँगसँगै (तपाईंको original strings.xml बाट लिएको)
- **Mobile-first design**: फोनमा राम्रोसँग देखिने गरी बनाइएको

---

## 🛠️ थप गर्न सकिने कुराहरू (भविष्यमा)

- Counter dashboard मा login/password (Firebase Auth)
- Order history / daily sales report
- Push notifications (FCM) — "Order ready" alert
- Multi-table QR code (हरेक टेबलको आफ्नै QR ले मेनु खुल्ने)
- Admin panel बाट menu items थप्ने/edit गर्ने (अहिले `src/App.jsx` को `MENU` array भित्रै छ)

---

## ❓ समस्या आयो भने

- **"Firebase Setup चाहियो" देखिन्छ**: `src/firebase.js` मा config सही राखिएको छैन।
- **Order देखिँदैन**: Firestore Rules ले block गरेको हुनसक्छ, Console मा Rules tab हेर्नुहोस्।
- **Build error**: `npm install` फेरि चलाउनुहोस्, Node.js version 18+ भएको सुनिश्चित गर्नुहोस्।
