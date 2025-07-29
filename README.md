# Tax
<!DOCTYPE html>
<html lang="th">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>ระบบคำนวณภาษีรถยนต์</title>
  <link rel="manifest" href="manifest.json" />
  <meta name="theme-color" content="#0066cc" />
  <style>
    body {
      font-family: sans-serif;
      max-width: 600px;
      margin: auto;
      padding: 20px;
    }
    label, select, input, button {
      display: block;
      width: 100%;
      margin-top: 10px;
      padding: 10px;
      font-size: 1rem;
    }
    button {
      background-color: #0066cc;
      color: white;
      border: none;
      cursor: pointer;
    }
    .result {
      margin-top: 20px;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <h1>ระบบคำนวณภาษีรถยนต์และรถจักรยานยนต์</h1>
  
  <label for="type">ประเภทรถ</label>
  <select id="type">
    <option value="car">รถยนต์นั่งส่วนบุคคล</option>
    <option value="pickup">รถกระบะ</option>
    <option value="van">รถตู้</option>
    <option value="motorcycle">รถจักรยานยนต์</option>
  </select>

  <label for="cc_or_weight">ซีซี หรือ น้ำหนัก (กิโลกรัม)</label>
  <input type="number" id="cc_or_weight" placeholder="เช่น 1600 หรือ 1200" />

  <label for="reg_date">วันที่จดทะเบียน</label>
  <input type="date" id="reg_date" />

  <label for="is_electric">เป็นรถมอเตอร์ไซค์ไฟฟ้า?</label>
  <select id="is_electric">
    <option value="no">ไม่ใช่</option>
    <option value="yes">ใช่</option>
  </select>

  <button onclick="calculate()">คำนวณ</button>

  <div class="result" id="output"></div>

  <script>
    function calculate() {
      const type = document.getElementById('type').value;
      const input = parseInt(document.getElementById('cc_or_weight').value);
      const regDate = new Date(document.getElementById('reg_date').value);
      const now = new Date();
      const years = Math.max(0, now.getFullYear() - regDate.getFullYear());
      const electric = document.getElementById('is_electric').value === 'yes';
      let tax = 0, prb = 0, inspection = 0, fine = 0;

      if (!input || isNaN(input)) {
        document.getElementById('output').innerHTML = 'กรุณากรอกข้อมูลซีซีหรือน้ำหนักให้ถูกต้อง';
        return;
      }
      if (!regDate.getTime()) {
        document.getElementById('output').innerHTML = 'กรุณาเลือกวันที่จดทะเบียนให้ถูกต้อง';
        return;
      }

      if (type === 'car') {
        if (input <= 600) tax = input * 0.5;
        else if (input <= 1800) tax = 600 * 0.5 + (input - 600) * 1.5;
        else tax = 600 * 0.5 + 1200 * 1.5 + (input - 1800) * 4.0;
        if (years >= 5) {
          let discountRate = 0;
          if (years === 5) discountRate = 0;
          else if (years === 6) discountRate = 10;
          else if (years === 7) discountRate = 20;
          else if (years === 8) discountRate = 30;
          else if (years === 9) discountRate = 40;
          else if (years >= 10) discountRate = 50;
          tax = tax * (1 - discountRate / 100);
        }
        prb = 645.21;
        inspection = 100;
      } else if (type === 'pickup') {
        if (input <= 500) tax = 300;
        else if (input <= 750) tax = 450;
        else if (input <= 1000) tax = 600;
        else if (input <= 1250) tax = 750;
        else if (input <= 1500) tax = 900;
        else if (input <= 1750) tax = 1050;
        else if (input <= 2000) tax = 1350;
        else if (input <= 2500) tax = 1650;
        else if (input <= 3000) tax = 1950;
        else if (input <= 3500) tax = 2250;
        else if (input <= 4000) tax = 2550;
        else if (input <= 4500) tax = 2850;
        else if (input <= 5000) tax = 3150;
        else if (input <= 6000) tax = 3450;
        else if (input <= 7000) tax = 3750;
        else tax = 4050;
        if (input > 3000) prb = 1220; else prb = 967.28;
        inspection = 100;
      } else if (type === 'van') {
        tax = 2000;
        prb = 1182.35;
        inspection = 100;
      } else if (type === 'motorcycle') {
        if (electric) prb = 350;
        else if (input <= 75) prb = 180;
        else if (input <= 125) prb = 350;
        else if (input <= 150) prb = 450;
        else prb = 645;
        tax = 100;
        inspection = 0;
      }

      const monthsLate = Math.max(0, (now.getFullYear() - regDate.getFullYear()) * 12 + (now.getMonth() - regDate.getMonth()) - 12);
      fine = monthsLate > 0 ? Math.round(tax * 0.01 * monthsLate) : 0;

      const total = tax + prb + inspection + fine;

      document.getElementById('output').innerHTML =
        `ค่าภาษี: ${tax.toFixed(2)} บาท<br>` +
        `ค่า พ.ร.บ.: ${prb.toFixed(2)} บาท<br>` +
        `ค่าตรวจสภาพ: ${inspection.toFixed(2)} บาท<br>` +
        `ค่าปรับ: ${fine.toFixed(2)} บาท<br>` +
        `<strong>รวมทั้งหมด: ${total.toFixed(2)} บาท</strong>`;
    }

    // Register service worker for offline support
    if ('serviceWorker' in navigator) {
      navigator.serviceWorker.register('service-worker.js').catch(console.error);
    }
  </script>
</body>
</html>
{
  "name": "ระบบคำนวณภาษีรถ",
  "short_name": "คำนวณภาษี",
  "start_url": "./",
  "scope": "./",
  "display": "standalone",
  "theme_color": "#0066cc",
  "background_color": "#ffffff",
  "icons": [
    {
      "src": "icon-192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "icon-512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}
const CACHE_NAME = 'tax-app-cache-v1';
const URLS_TO_CACHE = [
  './',
  './index.html',
  './manifest.json',
  './icon-192.png',
  './icon-512.png'
];

// Install SW and cache files
self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(cache => cache.addAll(URLS_TO_CACHE))
  );
});

// Intercept requests and serve cached files if available
self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request)
      .then(response => response || fetch(event.request))
  );
