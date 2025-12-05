# OPTION FOR NOTIFICATION

## 1. Firebase function
[step by step]
- login firebase console
- inisialisasi firebase function di project `firebase init functions`
- bikin cloud function `function/index.js` *disini bisa implement kirim berdasarkan lokasi atau prioritas
```dart
const functions = require("firebase-functions");
const admin = require("firebase-admin");

admin.initializeApp();
const db = admin.firestore();

// Haversine formula untuk hitung jarak
function distance(lat1, lon1, lat2, lon2) {
  const R = 6371;
  const dLat = (lat2 - lat1) * Math.PI / 180;
  const dLon = (lon2 - lon1) * Math.PI / 180;

  const a =
    Math.sin(dLat / 2) * Math.sin(dLat / 2) +
    Math.cos(lat1 * Math.PI / 180) *
    Math.cos(lat2 * Math.PI / 180) *
    Math.sin(dLon / 2) *
    Math.sin(dLon / 2);

  return R * 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
}

exports.sendEmergency = functions.https.onCall(async (data, context) => {
  const { userId, latitude, longitude, mode } = data;

  // Ambil semua helper
  const snap = await db.collection("users")
    .where("role", "==", "helper")
    .get();

  const helpers = [];

  snap.forEach(doc => {
    const d = doc.data();
    if (d.fcmToken && d.lat && d.lng) {
      helpers.push({
        id: doc.id,
        fcmToken: d.fcmToken,
        lat: d.lat,
        lng: d.lng,
        priorityLevel: d.priorityLevel || 1
      });
    }
  });

  if (helpers.length === 0) {
    return { message: "No helpers available" };
  }

  let sortedHelpers = [];

  // 🌍 SORTING BERDASARKAN LOKASI
  if (mode === "location") {
    sortedHelpers = helpers.sort((a, b) => {
      const distA = distance(latitude, longitude, a.lat, a.lng);
      const distB = distance(latitude, longitude, b.lat, b.lng);
      return distA - distB;
    });
  }

  // ⭐ SORTING BERDASARKAN PRIORITAS
  else if (mode === "priority") {
    sortedHelpers = helpers.sort((a, b) => {
      return b.priorityLevel - a.priorityLevel;
    });
  }

  // Jika tidak ada mode, default nearest
  else {
    sortedHelpers = helpers.sort((a, b) => {
      const distA = distance(latitude, longitude, a.lat, a.lng);
      const distB = distance(latitude, longitude, b.lat, b.lng);
      return distA - distB;
    });
  }

  // Ambil helper terbaik (misal 5 orang)
  const selected = sortedHelpers.slice(0, 5);
  const tokens = selected.map(h => h.fcmToken);

  // Kirim notifikasi FCM
  await admin.messaging().sendToDevice(tokens, {
    notification: {
      title: "Emergency Alert!",
      body: "Someone nearby needs help. Tap to respond."
    },
    data: {
      latitude: String(latitude),
      longitude: String(longitude),
      mode: mode,
      userId: userId,
    }
  });

  return {
    message: "Emergency notification sent!",
    sentTo: selected.map(h => h.id),
  };
});


```
- deploy function `firebase deploy --only functions`
- call function mirip kayak call API
```dart
final sendEmergency = FirebaseFunctions.instance.httpsCallable("sendEmergency");

await sendEmergency({
  "userId": userId,
  "latitude": userLat,
  "longitude": userLng,
  "mode": "location", // atau "priority"
});


```
- FCM (notifikasi) kekirim pake cloude functions

## 2. Backend with Firebase admin SDK
[step by step]
- bikin folder backend
- `npm init -y`
- `npm install express firebase-admin cors` *pokoknya setup backend node.js sesuai pengetahuan antum
- download firebase admin key, `firebase console -> project settings -> service accounts -> generate new private key`
- nanti keynya save di folder backend `serviceAccountKey.json`
- inisialisasi admin SDK
```js
const admin = require("firebase-admin");
admin.initializeApp({
  credential: admin.credential.cert(require("./serviceAccountKey.json"))
});
```
- bikin endpoint
```js
const express = require("express");
const app = express();
app.use(express.json());

app.post("/send", async (req, res) => {
  const { token, title, body } = req.body;
  await admin.messaging().send({
    token,
    notification: { title, body }
  });
  res.send("Sent!");
});
```
- run backend `node server.js`
- call api dari flutter
```dart
await http.post(
  Uri.parse("http://your-backend/send"),
  body: jsonEncode({
    "token": helperToken,
    "title": "Emergency!",
    "body": "Someone needs help"
  })
);
```

- tutorial: https://www.youtube.com/watch?v=J8j_jzWPRtw 