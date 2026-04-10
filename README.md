# Because it's Taiwan competition, Readme will be in Chinese

# 車輛預約管理系統

學校競賽專案。使用者可以預約、報到、歸還車輛；管理員可以管理車輛與查看預約清單。

[前端開發指引](./前端開發指引.md) — 各 API 的詳細請求 / 回應格式、欄位說明。

[壓力測試報告](./backend/test/壓力測試報告.md) — 100 並發壓力測試設計、結果與延遲分析。

---

## 技術棧

| 端 | 技術 |
|----|------|
| 後端 | NestJS · Prisma · BetterAuth · SQLite |
| 前端 | Angular · PrimeNG |

---

## 快速開始

### 後端

```bash
cd backend
npm run setup      # 第一次：安裝套件 + 建立資料庫 + 產生型別
npm run start:dev  # 啟動開發伺服器（port 3000）
```

> 之後只需要 `npm run start:dev`。
> 如果有人改了 `prisma/schema.prisma`，重新跑一次 `npm run setup`。

### 前端

```bash
cd frontend
npm install
ng serve           # 啟動開發伺服器（port 4200）
```

---

## 目錄結構

```
├── backend/          NestJS API 伺服器
│   ├── src/
│   │   ├── login/    登入 / 註冊
│   │   ├── vehicle/  車輛管理
│   │   ├── booking/  預約管理
│   │   ├── user/     使用者操作（報到、取消、歸還）
│   │   ├── common/   共用守衛（AuthGuard）
│   │   └── prisma/   資料庫服務
│   └── prisma/
│       └── schema.prisma
└── frontend/         Angular 前端
    └── src/
        └── app/
```

---

## API 總覽

| 方法 | 路徑 | 說明 | 需登入 |
|------|------|------|--------|
| POST | `/api/login/register` | 註冊 | |
| POST | `/api/login` | 登入 | |
| GET  | `/api/vehicle/availability` | 查詢可預約狀況 | ✓ |
| POST | `/api/vehicle/add` | 新增車輛 | ✓ |
| POST | `/api/vehicle/delete` | 刪除車輛 | ✓ |
| GET  | `/api/vehicle/bookings` | 查詢預約者清單 | ✓ |
| POST | `/api/booking` | 建立預約 | ✓ |
| GET  | `/api/booking/conflicts` | 查詢衝突時段 | ✓ |
| GET  | `/api/user/bookings` | 我的預約 | ✓ |
| POST | `/api/user/check-in` | 報到 | ✓ |
| POST | `/api/user/cancel` | 取消預約 | ✓ |
| POST | `/api/user/return` | 歸還車輛 | ✓ |

詳細請求 / 回應格式見 [前端開發指引.md](./前端開發指引.md)。

---

## 資料模型

```
User          使用者
Vehicle       車輛（type / id）
Booking       進行中的預約（PENDING → ACTIVE → OVERDUE）
BookingRecord 完成或取消的歷史記錄（含 ViolationType）
```

**預約狀態：** `PENDING` → `ACTIVE` → 完成（移入 BookingRecord）

**違規類型：** `NONE` / `LATE`（遲到報到）/ `NO_SHOW`（未到場）/ `OVERDUE`（超時未還）
