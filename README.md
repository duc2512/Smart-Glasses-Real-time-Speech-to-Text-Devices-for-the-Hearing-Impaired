<div align="center">

# 👓 Smart Glasses — Real-time Speech-to-Text
### Mắt Kính Thông Minh Hỗ Trợ Người Khiếm Thính

[![ESP32](https://img.shields.io/badge/ESP32-Firmware-blue?style=for-the-badge&logo=espressif&logoColor=white)](https://www.espressif.com/)
[![Python](https://img.shields.io/badge/Python-3.8+-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org/)
[![Flask](https://img.shields.io/badge/Flask-Web_Portal-000000?style=for-the-badge&logo=flask&logoColor=white)](https://flask.palletsprojects.com/)
[![WebSocket](https://img.shields.io/badge/WebSocket-Real--time-orange?style=for-the-badge)](https://websockets.readthedocs.io/)
[![Speechmatics](https://img.shields.io/badge/Speechmatics-STT_API-purple?style=for-the-badge)](https://www.speechmatics.com/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Database-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)](https://postgresql.org/)

**Hệ thống nhận dạng giọng nói tiếng Việt real-time dành cho người khiếm thính — ESP32 thu âm & stream audio qua WebSocket, server Python xử lý với Speechmatics API, hiển thị transcript trên Web Portal.**

**Real-time Vietnamese speech recognition system for the hearing impaired — ESP32 captures & streams audio via WebSocket, Python server processes with Speechmatics API, displaying transcripts on a Web Portal.**

</div>

---

## 📸 Demo

<!-- 
🔽 THÊM HÌNH ẢNH / VIDEO DEMO TẠI ĐÂY 🔽
Ví dụ:
![Demo](docs/demo.gif)
![Smart Glasses](docs/glasses.jpg)
![Web Portal](docs/portal.png)
-->

> ⚠️ *Vui lòng thêm ảnh/video demo mắt kính và web portal tại đây.*
>
> ⚠️ *Please add smart glasses & web portal demo images/videos here.*

---

## 📐 System Architecture / Kiến Trúc Hệ Thống

```mermaid
graph LR
    subgraph GLASSES ["👓 Smart Glasses (ESP32)"]
        MIC[I2S Microphone<br/>INMP441]
        ESP[ESP32<br/>Audio Capture]
    end

    subgraph SERVER ["🖥️ Backend Server"]
        WS_SERVER[Audio Server<br/>Python WebSocket<br/>Port 8765]
        SPEECH_API[Speechmatics API<br/>Vietnamese STT<br/>Enhanced Mode]
        FLASK[Flask Web Portal<br/>Port 5000]
        DB[(PostgreSQL<br/>Transcripts + Devices)]
    end

    subgraph TUNNEL ["🔒 Secure Access"]
        CF[Cloudflare Tunnel<br/>Public HTTPS]
    end

    subgraph CLIENT ["👥 End Users"]
        PORTAL[🌐 Web Portal<br/>Device Management]
        TRANSCRIPT[📝 Live Transcript<br/>Partial + Final]
        HISTORY[📋 Transcript History<br/>24h]
    end

    MIC -->|I2S 16kHz<br/>16-bit PCM| ESP
    ESP -->|WebSocket<br/>768-byte chunks| WS_SERVER
    WS_SERVER -->|Audio Stream| SPEECH_API
    SPEECH_API -->|Transcript JSON| WS_SERVER
    WS_SERVER --> DB
    WS_SERVER --> FLASK
    FLASK --> PORTAL
    FLASK --> TRANSCRIPT
    FLASK --> HISTORY
    FLASK <--> DB
    CF --> FLASK

    style GLASSES fill:#4CAF50,color:#fff
    style SERVER fill:#2196F3,color:#fff
    style TUNNEL fill:#FF9800,color:#fff
    style CLIENT fill:#9C27B0,color:#fff
```

---

## 🛠️ Tech Stack / Công Nghệ

| Layer | Technology |
|-------|-----------|
| **Embedded** | ![ESP32](https://img.shields.io/badge/ESP32-blue?style=flat-square&logo=espressif&logoColor=white) ![C++](https://img.shields.io/badge/C++-13%25-00599C?style=flat-square&logo=cplusplus&logoColor=white) ![I2S](https://img.shields.io/badge/I2S-Audio-green?style=flat-square) |
| **Backend** | ![Python](https://img.shields.io/badge/Python-41.1%25-3776AB?style=flat-square&logo=python&logoColor=white) ![Flask](https://img.shields.io/badge/Flask-000000?style=flat-square&logo=flask&logoColor=white) |
| **STT Engine** | ![Speechmatics](https://img.shields.io/badge/Speechmatics-Enhanced-purple?style=flat-square) |
| **Real-time** | ![WebSocket](https://img.shields.io/badge/WebSocket-Port_8765-orange?style=flat-square) |
| **Database** | ![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=flat-square&logo=postgresql&logoColor=white) ![psycopg2](https://img.shields.io/badge/psycopg2-adapter-blue?style=flat-square) |
| **Frontend** | ![HTML](https://img.shields.io/badge/HTML5-33.7%25-E34F26?style=flat-square&logo=html5&logoColor=white) ![Bootstrap](https://img.shields.io/badge/Bootstrap-7952B3?style=flat-square&logo=bootstrap&logoColor=white) ![AJAX](https://img.shields.io/badge/AJAX-Async-blue?style=flat-square) |
| **Infrastructure** | ![Cloudflare](https://img.shields.io/badge/Cloudflare_Tunnel-F38020?style=flat-square&logo=cloudflare&logoColor=white) |
| **Auth** | ![bcrypt](https://img.shields.io/badge/bcrypt-Password_Hashing-red?style=flat-square) |

---

## ⚡ Key Features & Metrics / Tính Năng & Chỉ Số

| Feature | Metric |
|---------|--------|
| 🎙️ **Audio Pipeline** | Tối ưu hóa pipeline xử lý âm thanh: **16kHz, 16-bit PCM** signed little-endian, chunk size **768 bytes** |
| ⚡ **Độ trễ thấp** / Low Latency | Truyền audio qua WebSocket với `max_delay = 0.7s`, operating point **"enhanced"** |
| 🇻🇳 **Nhận dạng tiếng Việt** / Vietnamese STT | Speechmatics API — hiển thị **partial** (tạm thời) + **final** (hoàn chỉnh) transcript real-time |
| 📱 **Quản lý đa thiết bị** / Multi-device | Đăng ký & quản lý nhiều ESP32, theo dõi trạng thái **online/offline** (IP, MAC, RSSI) |
| 🌐 **Cấu hình WiFi từ xa** / Remote WiFi Config | Cập nhật WiFi credentials cho ESP32 qua web portal — ESP32 tự động apply khi reboot |
| 📋 **Lịch sử transcript** / Transcript History | Lưu trữ toàn bộ vào PostgreSQL, xem **24h gần nhất**, phân biệt partial/final |
| 🔐 **Bảo mật** / Security | **bcrypt** password hashing, device ownership isolation, session management |
| 💓 **Heartbeat tối ưu** / Optimized Heartbeat | Interval **120s**, timeout **60s** — giảm tải server, phát hiện offline nhanh |
| 🌍 **Truy cập từ xa** / Remote Access | **Cloudflare Tunnel** → public HTTPS, không cần port-forwarding |

---

## 🔌 Hardware Setup / Sơ Đồ Đấu Nối

### Pinout Table / Bảng Nối Chân

| Component | Pin / Signal | ESP32 GPIO |
|-----------|-------------|------------|
| **INMP441 (I2S Mic)** | WS (Word Select) | `GPIO 25` |
| | SCK (Clock) | `GPIO 26` |
| | SD (Data) | `GPIO 33` |
| | VDD | `3.3V` |
| | GND | `GND` |
| | L/R | `GND` (Left channel) |

```
  ┌─────────────┐         ┌──────────────────────┐
  │  INMP441     │         │      ESP32            │
  │  I2S Mic     │         │                       │
  │   WS ────────┼────────►│ GPIO 25              │
  │   SCK ───────┼────────►│ GPIO 26              │
  │   SD ────────┼────────►│ GPIO 33              │
  │   VDD ───────┼────────►│ 3.3V                 │
  │   GND ───────┼────────►│ GND                  │
  │   L/R ───────┼────────►│ GND                  │
  └─────────────┘         │                       │
                           │  WiFi ──► WebSocket   │
                           │          (ws://IP:8765)│
                           └──────────────────────┘
```

> 💡 **Note:** ESP32 thu âm liên tục với I2S ở **16kHz sample rate**, gửi qua WebSocket dưới dạng PCM 16-bit chunks.

---

## 🚀 How to Run / Hướng Dẫn Chạy

### 1. Prerequisites / Yêu cầu
```
• Python 3.8+
• PostgreSQL 12+
• Cloudflared CLI (optional, cho remote access)
• ESP32 + INMP441 I2S Microphone
• Speechmatics API Key (https://www.speechmatics.com/)
```

### 2. Installation / Cài đặt

```bash
# Clone repository
git clone https://github.com/duc2512/Smart-Glasses-Real-time-Speech-to-Text-Devices-for-the-Hearing-Impaired.git
cd Smart-Glasses-Real-time-Speech-to-Text-Devices-for-the-Hearing-Impaired

# Tạo virtual environment
python -m venv .venv
.venv\Scripts\activate          # Windows
# source .venv/bin/activate     # Linux/Mac

# Cài đặt dependencies
pip install -r web_portal/requirements.txt
pip install speechmatics websockets httpx
```

### 3. Database Setup

```sql
-- Tạo database PostgreSQL
psql -U postgres
CREATE DATABASE esp32_management;
\c esp32_management
\i database/schema.sql
```

### 4. Configuration / Cấu hình

Tạo file `.env` trong thư mục `web_portal/`:
```env
DB_HOST=localhost
DB_NAME=esp32_management
DB_USER=postgres
DB_PASSWORD=your_password
DB_PORT=5432
SECRET_KEY=your_secret_key
```

Cập nhật Speechmatics API Key:
```python
# Trong file final_lap/esp32_realtime_server.py:
API_KEY = "your_speechmatics_api_key"
```

### 5. Khởi chạy / Start

```bash
# Khởi động tất cả dịch vụ / Start all services
start_all.bat

# Tự động khởi động:
# 1. ESP32 Audio Server (port 8765)
# 2. Cloudflare Tunnel (public access)
# 3. Web Portal (port 5000)
```

### 6. Truy cập / Access

| Method | URL |
|--------|-----|
| **Local** | `http://localhost:5000` |
| **LAN** | `http://<your-ip>:5000` |
| **Public** | `https://esp32.ptitavitech.online` (Cloudflare Tunnel) |

---

## 📁 Project Structure / Cấu Trúc Dự Án

```
Smart-Glasses-.../
├── database/                          # 🗄️ Database
│   ├── schema.sql                     # PostgreSQL schema
│   ├── add_speaker_column.sql         # Migration: speaker column
│   └── MIGRATION_GUIDE.md            # Migration guide
│
├── final_lap/                         # 🚀 Production Server
│   ├── esp32_realtime_server.py       # Main audio WebSocket server
│   ├── config.yml                     # Cloudflare Tunnel config
│   └── ESP32_Realtime_Streaming/      # ESP32 firmware (C++)
│
├── server_lap/                        # 🧪 Development Server
│   └── lap_realtime_server.py         # Test server
│
├── web_portal/                        # 🌐 Flask Web Application
│   ├── app.py                         # Main Flask app
│   ├── requirements.txt              # Python dependencies
│   └── templates/                     # HTML templates (Bootstrap)
│
└── start_all.bat                      # ▶️ One-click startup
```

---

## 🔗 API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/devices` | Đăng ký thiết bị mới / Register device |
| `GET` | `/api/devices` | Danh sách thiết bị / List devices |
| `PUT` | `/api/devices/<id>/wifi` | Cập nhật WiFi config / Update WiFi |
| `GET` | `/api/transcripts/<device_id>` | Lịch sử transcript / Transcript history |
| `WS` | `ws://server:8765` | Audio streaming (ESP32 → Server) |

---

## 👤 Author

**Le Tho Duc** — [GitHub @duc2512](https://github.com/duc2512)

---

<div align="center">
⭐ If you find this project useful, please give it a star! ⭐
</div>
