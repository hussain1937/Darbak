Markdown# Darbak — Logistics & Freight Bidding Platform

Darbak is a full-stack logistics platform that connects **shippers** who need to
move freight with **drivers / fleet operators** who bid to transport it.

> Graduation project — Bachelor of Computer Science.
> **Developed & Engineered by [Hussain Emad Mash](https://github.com/hussain1937) and the team.**

## Screenshots

### Mobile Application EN

![Darbak Mobile App EN](https://media.discordapp.net/attachments/1406067294211145809/1513490997248786552/image.png?ex=6a293d55&is=6a27ebd5&hm=777efee666fce31ec3387e4c645215f81bb629ce7901f8cdf395b07f6c9950b7&=&format=webp&quality=lossless&width=2500&height=938)

### Mobile Application AR

![Darbak Mobile App AR](https://media.discordapp.net/attachments/1406067294211145809/1513491256435539978/image.png?ex=6a293d92&is=6a27ec12&hm=ceee617433f045ed894c931017eaf64a3ddb74a64c3ced39e4004c40542d58a6&=&format=webp&quality=lossless&width=2500&height=956)

---

## 1. Feature Highlights

| Module                  | Description                                                                  |
| ----------------------- | ---------------------------------------------------------------------------- |
| Authentication          | Email/phone + password, Firebase Auth bridge, password reset, JWT sessions.  |
| KYB Verification        | Shippers upload commercial registration, drivers upload license + insurance. |
| Shipment Marketplace    | Shippers post shipments with origin/destination, weight, truck requirements. |
| Bidding / Live Auction  | Drivers submit bids; bidding rooms broadcast events over Socket.IO.          |
| Truck Catalog           | Hierarchical truck classification (group → category → variant) with rules.   |
| Job Tracking            | Shipment lifecycle (assigned → in_transit → delivered) with status events.   |
| EPOD                    | Driver uploads electronic proof of delivery photos to S3.                    |
| In-App Chat             | Direct conversations + shipment-scoped chats with images, videos, location.  |
| Ratings                 | Bidirectional ratings with aggregated star averages per user.                |
| Notifications           | Firebase Cloud Messaging push + in-app notification center.                  |
| Late-Delivery Penalties | Daily cron sweep applies penalty rules with caps.                            |
| Admin Dashboard         | KYB queue, user moderation, shipment overview, real-time stats (SPA).        |
| Contract PDF            | Auto-generated Arabic PDF contracts stored in S3 (puppeteer).                |

---

## 2. Tech Stack

| Layer             | Technologies                                                              |
| ----------------- | ------------------------------------------------------------------------- |
| Mobile / Web      | Flutter (Dart 3.10+), Material 3, RTL Arabic, Firebase Auth/Core          |
| Backend API       | Node.js 22, Express, JWT, bcryptjs, express-validator                     |
| Real-time         | Socket.IO (server + client)                                               |
| Database          | MySQL 8 / MariaDB (XAMPP-compatible) via `mysql2/promise`                 |
| Object Storage    | MinIO (S3-compatible) via `@aws-sdk/client-s3` + `multer-s3`              |
| Push Notifications| Firebase Cloud Messaging (`firebase-admin`)                               |
| Email Auth        | Firebase Auth REST (`sendOobCode` password reset)                         |
| PDF               | Puppeteer (server-side Arabic contracts)                                  |
| Scheduled Jobs    | `node-cron` (daily late-delivery penalty sweep)                           |
| Testing           | Jest + Supertest (backend), `flutter_test` + Playwright (Flutter + E2E)   |
| CI                | GitHub Actions — see `.github/workflows/qa.yml`                           |

---

## 3. Repository Layout

Darbak1/├── android/                        # Flutter Android project├── ios/                            # Flutter iOS project├── linux/  macos/  windows/  web/  # Other Flutter targets├── assets/                         # App fonts, icons, animations├── lib/                            # Flutter Dart source (screens, services, models, widgets)├── test/                           # Flutter unit + widget tests├── integration_test/               # Flutter integration / smoke tests├── tool/                           # Dart + Node helpers (test report, lcov tools)│├── backend/                        # Node.js / Express API│   ├── app.js                      # Express entrypoint (HTTP + Socket.IO + cron)│   ├── config/db.js                # MySQL connection pool│   ├── controllers/                # Route handlers│   ├── models/                     # SQL data access│   ├── routes/                     # Express routers│   ├── middleware/                 # JWT, admin, KYB middleware│   ├── services/                   # Business services (PDF, payout, profile)│   ├── socket/                     # chatSocket (Socket.IO config)│   ├── jobs/                       # Cron jobs (delivery penalty)│   ├── utils/                      # Crypto, S3, FCM, schema ensurers, validators│   ├── constants/                  # Shared catalogs (truck classification, etc.)│   ├── admin_portal/               # Static admin SPA (index.html)│   ├── sql/                        # darbak_db.sql + migration history│   ├── tests/                      # Jest unit / integration / socket tests│   ├── uploads/epod/               # Local fallback for EPOD photos (S3 preferred)│   ├── package.json│   ├── jest.config.js│   └── .env.example                # Copy to .env and edit│├── docker-compose.yml              # MinIO + minio_init bootstrap├── docker/minio-cors.json          # CORS rules applied to MinIO buckets│├── e2e/admin-dashboard/            # Playwright cross-browser E2E for admin SPA├── docs/                           # Documentation│   ├── MINIO_S3_SETUP.md           # Detailed MinIO setup walkthrough│   ├── TESTING.md                  # QA architecture and commands│   └── development-history/        # Internal feature design + bug-fix logs│├── .github/workflows/qa.yml        # CI: backend + Flutter + unified QA report├── pubspec.yaml                    # Flutter dependencies└── README.md                       # You are here
---

## 4. Prerequisites

| Tool                            | Version       | Why                                              |
| ------------------------------- | ------------- | ---------------------------------------------- |
| Git                             | any recent    | Clone the repo                                 |
| Node.js                         | **>= 22 LTS** | Backend runtime (CI pinned to Node 22)         |
| npm                             | bundled       | Install backend deps                           |
| Flutter SDK                     | **3.x**       | Mobile / web client (Dart `^3.10.8`)           |
| MySQL or MariaDB (XAMPP works)  | 8.x / 10.x    | Persistence                                    |
| Docker Desktop / Docker Engine  | any recent    | Run MinIO (S3) locally                         |
| (Optional) Firebase project     | -             | Real push + password reset (otherwise mocked)  |
| (Optional) Java JDK 17          | -             | Android builds                                 |
| (Optional) Xcode                | latest        | iOS / macOS builds                             |

---

## 5. First-Time Setup

### 5.1 Clone

```bash
git clone [https://github.com/](https://github.com/)<your-org>/Darbak1.git
cd Darbak1
5.2 Create the databaseStart your MySQL / MariaDB server (XAMPP → Start MySQL).Create an empty database:SQLCREATE DATABASE darbak_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
Import the schema:Bashmysql -u root -p darbak_db < backend/sql/darbak_db.sql
The backend/sql/Backup/ folder contains the incremental migration files(migrations_01..18) that produced this schema, kept as history.5.3 Configure environment variablesBashcd backend
cp .env.example .env       # PowerShell users: copy .env.example .env
Open backend/.env and fill in the values. The most important ones are:VariableNotesMYSQL_*Match your local DB credentialsJWT_SECRETLong random stringENCRYPTION_KEY / ENCRYPTION_IVAES-256-CBC: 64 hex + 32 hex (generators are in .env.example)MINIO_ENDPOINT127.0.0.1:9190 — API port, never the console port 9191MINIO_EXTERNAL_URLSame machine: http://127.0.0.1:9190 — phones on LAN: http://<IP>:9190MINIO_BUCKETdarbak-uploads (auto-created by minio_init)FIREBASE_SERVICE_ACCOUNT_PATHPath to your Firebase Admin service account JSON (kept out of git)FIREBASE_WEB_API_KEYFirebase Console → Project settings → General → Web API Key5.4 Start MinIO (S3-compatible storage)From the project root (folder containing docker-compose.yml):Bashdocker compose up -d minio
docker compose run --rm minio_init        # creates buckets + CORS, runs once
Full walkthrough, troubleshooting and LAN setup: docs/MINIO_S3_SETUP.md.5.5 Firebase (optional but recommended)Create a Firebase project (or reuse one).Download the service account JSON fromProject settings → Service accounts → Generate new private key.Save it outside the repository, then pointFIREBASE_SERVICE_ACCOUNT_PATH in backend/.env to that path.Run flutterfire configure once to regenerate lib/firebase_options.dartwith your project's identifiers.Without Firebase, server-side push and Firebase login are no-ops; the app stillruns against email/phone + password auth.5.6 Install backend dependenciesBashcd backend
npm install
5.7 Install Flutter dependenciesFrom the project root:Bashflutter pub get
5.8 Bootstrap an admin user (one-off)Bashcd backend
# PowerShell
$env:ADMIN_PHONE="0500000000"; $env:ADMIN_PASSWORD="StrongPass!1"; node utils/fix_admin.js
# bash
ADMIN_PHONE=0500000000 ADMIN_PASSWORD='StrongPass!1' node utils/fix_admin.js
Then sign in to the admin portal at http://localhost:5000/admin.6. Running the ProjectBackend APIBashcd backend
npm run dev       # nodemon (auto-restart on file change)
# or
npm start         # production mode (`node app.js`)
The API listens on http://localhost:5000 by default. A green log line[S3] HeadBucket OK — API reachable. confirms MinIO is reachable.Admin Dashboard (SPA)Open http://localhost:5000/admin in a browser. The SPA is served frombackend/admin_portal/index.html.Flutter AppBashflutter run                       # default device
flutter run -d chrome             # web (web target uses 127.0.0.1)
flutter run -d <android-emulator> # Android emulator (host = 10.0.2.2)
The Flutter ApiService.baseUrl resolves to http://10.0.2.2:5000/api forAndroid emulators (maps to host) and http://127.0.0.1:5000/api for web. For aphysical phone, edit lib/api_service.dart to point at your PC's LAN IP andmake sure your firewall allows inbound port 5000.7. TestingA full QA guide lives in docs/TESTING.md. Quick reference:Backend (Jest)Bashcd backend
npm test                  # all suites
npm run test:unit         # unit tests only
npm run test:integration  # Express route tests with mocked DB / S3 / Firebase
npm run test:socket       # Socket.IO chat tests
npm run test:coverage     # HTML + LCOV at backend/coverage/
npm run test:ci           # used by GitHub Actions
Strict per-file coverage thresholds (encryption, auctionLive, late-deliverypenalty, truck classification, auth middleware, …) are enforced inbackend/jest.config.js.FlutterFrom the project root:Bashflutter analyze --no-fatal-infos --no-fatal-warnings
flutter test
flutter test --coverage
dart run tool/check_lcov_min.dart coverage/lcov.info --min=7
flutter test integration_test          # requires emulator/device
Admin Dashboard E2E (Playwright)Bashcd e2e/admin-dashboard
npm install
npx playwright install          # first time only
npm test                        # all browsers
npm run test:chromium           # one browser
Unified QA HTML reportBashnode tool/generate_test_report.js
# Open docs/test-report/index.html in any browser.
8. Security & Secrets.env is git-ignored and must never be committed.Firebase Admin service account JSON files matching *firebase-adminsdk*.jsonare git-ignored.The Firebase Web API key present in lib/firebase_options.dart andandroid/app/google-services.json is a public client identifier (not asecret) and is meant to ship with the app; security is enforced by FirebaseAuth rules and backend JWT verification.backend/utils/fix_admin.js does not contain credentials; pass them viaenvironment variables when seeding the admin user.Default MinIO credentials (admin / password123) in docker-compose.ymlare for local development only. Change them for any shared deployment.9. Continuous Integration.github/workflows/qa.yml runs on push and PR to main / master / develop:Backend job — npm ci + npm run test:ci (Jest + coverage).Flutter job — flutter pub get + flutter analyze + flutter test --coverage.QA report job — builds the unified HTML dashboard and uploads it as anartefact.Coverage artefacts (backend-coverage, flutter-coverage, qa-dashboard) areattached to each workflow run.10. TroubleshootingSymptomLikely cause / fixفشل في الاتصال بقاعدة البيانات at startupMySQL not running, wrong credentials, or DB not created. Check backend/.env.S3 API Requests must be made to API portMINIO_ENDPOINT is using the console port (9191). Use 9190.Phone cannot open EPOD/chat image URLsMINIO_EXTERNAL_URL uses localhost; switch to your PC's LAN IP.Bearer token rejected with 401JWT_SECRET differs between encoder and verifier; restart backend after edit.Flutter web build cannot reach APIAdd http://127.0.0.1:5000 to allow-listed origins; CORS already wildcarded.Firebase login returns 500Service account path wrong or file missing. Check the log line at startup.11. Documentation Indexdocs/MINIO_S3_SETUP.md — Complete MinIO setup, port cheat sheet, and troubleshooting.docs/TESTING.md — Test architecture, commands, and coverage policy.docs/development-history/ — Feature design notes and bug-fix reports.e2e/admin-dashboard/README.md — Playwright suite details.12. License & AcknowledgementsSubmitted as a graduation project. All rights reserved by the author. Third-partydependencies retain their original licenses (see backend/package.json andpubspec.yaml).
