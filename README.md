# DCA Planner — Dividend Portfolio

เครื่องมือวางแผน DCA รายสัปดาห์สำหรับพอร์ตหุ้นปันผล 5 ตัว ออกแบบมาให้ใช้งานผ่าน GitHub Pages โดยไม่ต้องติดตั้งอะไรเพิ่ม

## Demo

```
https://<username>.github.io/<repo-name>
```

---

## Features

- **ตารางซื้อรายสัปดาห์** — แบ่งงบเดือนออกเป็นสัปดาห์ตามวันอาทิตย์จริงของเดือนนั้น งบไม่เท่ากันทุกสัปดาห์ (ต้นเดือนมากกว่า) เพื่อให้เงินทำงานได้นานกว่า
- **Highlight สัปดาห์ปัจจุบัน** — เปิดมาเห็นทันทีว่าสัปดาห์นี้ต้องซื้ออะไร เท่าไหร่
- **นาฬิกา live** — แสดงวันที่และเวลาจริงจากเครื่อง พร้อม timezone
- **ปฏิทิน ex-dividend** — แสดงเดือนที่แต่ละหุ้นมี ex-date อ้างอิงจากข้อมูลจริง
- **Zero dependency** — ไม่มี framework, ไม่มี build step, ไม่มี API call

---

## พอร์ตที่ใช้

| Ticker | Sector | สัดส่วน |
|--------|--------|---------|
| ENB | Pipeline & Utility | 25% |
| BIP | Global Infrastructure | 20% |
| O | REIT (จ่ายรายเดือน) | 25% |
| DUK | Electric Utility | 15% |
| EPD | Midstream MLP | 15% |

---

## ข้อมูลที่คำนวณได้จริง vs ข้อมูลที่เปลี่ยนแปลง

หน้านี้แสดงเฉพาะข้อมูลที่คำนวณได้แน่นอนจากสิ่งที่ผู้ใช้ใส่เข้ามา

| แสดง | ไม่แสดง | เหตุผล |
|------|---------|--------|
| ยอดซื้อรายสัปดาห์ (฿) | Dividend Yield | เปลี่ยนทุกวัน |
| จำนวนสัปดาห์ในเดือน | ปันผล/ปี | ไม่รู้พอร์ตสะสมเท่าไหร่ |
| เดือน ex-dividend | อัตราแลกเปลี่ยน | เปลี่ยนตลอด |

---

## ข้อมูล Ex-Dividend

อ้างอิงจาก [stockanalysis.com](https://stockanalysis.com) ข้อมูล ณ มีนาคม 2026

| Ticker | Ex-Dividend เดือน | หมายเหตุ |
|--------|------------------|---------|
| ENB | ก.พ / พ.ค / ส.ค / พ.ย | |
| BIP | ก.พ / พ.ค / ส.ค / พ.ย | |
| O | ทุกเดือน | Monthly dividend |
| DUK | ก.พ / พ.ค / ส.ค / พ.ย | |
| EPD | ม.ค / เม.ย / ก.ค / ต.ค | |

> วันที่แน่นอนในแต่ละไตรมาสอาจเลื่อนได้ 1–3 วัน บริษัทประกาศล่วงหน้าทีละไตรมาส

---

## วิธีใช้งาน

### ใช้งานตรง (ไม่ต้อง deploy)

เปิดไฟล์ `index.html` ในเบราว์เซอร์ได้เลย

### Deploy บน GitHub Pages

1. สร้าง repository ใหม่บน GitHub
2. วางไฟล์ `index.html` ที่ root ของ repo
3. ไปที่ **Settings → Pages → Source** เลือก branch `main` แล้ว Save
4. รอ 1–2 นาที จะได้ URL: `https://<username>.github.io/<repo-name>`

```
your-repo/
├── index.html
└── README.md
```

---

## การปรับแต่ง

### เปลี่ยนหุ้นหรือสัดส่วน

แก้ `STOCKS` array ใน `index.html`:

```js
const STOCKS = [
  { ticker:'ENB', sector:'Pipeline', color:'var(--enb)', hex:'#5b8fd4', alloc:0.25 },
  { ticker:'BIP', sector:'Infra',    color:'var(--bip)', hex:'#4bba8a', alloc:0.20 },
  // เพิ่ม/ลด/แก้ตรงนี้
];
```

> `alloc` ทุกตัวรวมกันต้องได้ `1.0` พอดี

### เปลี่ยนสี

แก้ CSS variables ใน `:root`:

```css
:root {
  --enb: #5b8fd4;
  --bip: #4bba8a;
  --o:   #e08a45;
  --duk: #a070c8;
  --epd: #d45858;
}
```

### อัปเดต Ex-Dividend Schedule

แก้ `DIV_SCHEDULE` และ `DIV_DATA_DATE`:

```js
const DIV_SCHEDULE = {
  ENB: [1, 4, 7, 10],   // index เดือน 0=ม.ค, 1=ก.พ, ...
  O:   [0,1,2,3,4,5,6,7,8,9,10,11],
  // ...
};
const DIV_DATA_DATE = 'มี.ค 2026'; // อัปเดตวันที่ที่ตรวจสอบข้อมูล
```

---

## Technical Notes

- **DST-safe date arithmetic** — `getSundays()` ใช้ noon (12:00) เป็น anchor เพื่อป้องกัน edge case จาก Daylight Saving Time ที่อาจทำให้วันเปลี่ยนในบางปีหรือบาง timezone
- **Year range** — รองรับถึงปีปัจจุบัน +200 ปี คำนวณ dynamic ตอน load ไม่ hardcode
- **Date-only comparison** — `isCurrentWeek()` strip เวลาออกก่อนเปรียบเทียบ ป้องกัน false negative จาก timestamp

---

## ข้อจำกัด

- ข้อมูล ex-dividend เป็น **เดือน** เท่านั้น ไม่ใช่วันที่แน่นอน
- ไม่รวม withholding tax ที่หักจากประเทศต้นทาง (US/Canada)
- ไม่มีข้อมูล real-time ราคาหุ้นหรืออัตราแลกเปลี่ยน
- **ไม่ใช่คำแนะนำการลงทุน**

---

## License

MIT