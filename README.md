# Jockie Music Lavalink Fly v5.3.0

Bot Discord.js 14 + lavalink-client 2.10.3. Thiết kế cho:

- Bot chạy Fly.io
- Lavalink chạy Render
- Render public WSS qua port 443, Lavalink nội bộ 2333

## 1. Environment của BOT

```text
DISCORD_TOKEN=TOKEN_DISCORD
LAVALINK_HOST=lava-xxxx.onrender.com
LAVALINK_PORT=443
LAVALINK_SECURE=true
LAVALINK_PASSWORD=MẬT_KHẨU_LAVALINK
DEFAULT_PREFIX=!
PLAYING_ICON=🐱
BOT_STATUS=Music
PORT=8080
DATA_DIR=/data
PLAYER_DEBUG=false
```

LAVALINK_HOST chỉ ghi hostname. Không ghi `https://`.

## 2. Loop đơn giản

Chỉ cần dùng một lệnh:

```text
!loop
```

Mỗi lần bấm sẽ chuyển:

```text
LẦN 1: 🔂 LOOP 1 BÀI
       ↓
LẦN 2: 🔁 LOOP QUEUE
       ↓
LẦN 3: 🔁 OFF
       ↓
LẦN 4: 🔂 LOOP 1 BÀI
```

`!lp` là alias của `!loop`.

Vẫn hỗ trợ:

```text
!loop track
!loop queue
!loop off
```

nhưng không cần dùng nếu muốn thao tác nhanh.

## 3. Commands

```text
!play / !p <tên hoặc URL>
!pause / !pa
!resume / !r
!skip / !s
!previous / !prev
!stop
!leave / !lv
!nowplaying / !np
!queue / !q
!volume 0-100
!seek 1:30
!loop / !lp
!shuffle
!remove <số>
!clear
!join
!ping
!prefix <ký tự>
!prefix reset
```

## 4. Fix lỗi playlist tự next liên tục

Bot sử dụng `autoSkip: true` của lavalink-client nhưng **không gọi `player.play()` trong `trackEnd`**. Chỉ gọi `player.play()` một lần khi player đang idle sau khi queue được thêm track.

Nếu Lavalink trả track error/stuck liên tiếp 3 lần trong 30 giây, bot dừng queue để tránh vòng lặp next vô hạn.

## 5. Silent notification

Các tin nhắn bot như Started playing và Added Playlist sử dụng Discord `SuppressNotifications`, nên vẫn xuất hiện trong room nhưng không tạo notification âm thanh/push.

## 6. Deploy Fly

Đổi:

```toml
app = "CHANGE-ME"
```

thành tên Fly app của bạn.

Tạo volume:

```bash
fly volumes create bot_data --size 1 --region sin
```

Set secret:

```bash
fly secrets set DISCORD_TOKEN="..." LAVALINK_HOST="lava-xxxx.onrender.com" LAVALINK_PASSWORD="..."
```

Sau đó:

```bash
fly deploy
```

## 7. Discord Developer Portal

Bật Message Content Intent.

Bot cần quyền:

- View Channel
- Send Messages
- Embed Links
- Connect
- Speak
- Use Voice Activity

## 8. Lavalink Render

Lavalink phải public bằng HTTPS/WSS. Với Render, bot kết nối:

```text
host = lava-xxxx.onrender.com
port = 443
secure = true
```

Không dùng:

```text
host = https://lava-xxxx.onrender.com
port = 2333
secure = false
```

Port 2333 là port Lavalink bên trong container Render.
