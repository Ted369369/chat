# Python MQTT Chat Demo

這是一個不用後端的 Python 桌面聊天室 demo。

技術：

- Python
- Qt6 UI：`PyQt6`
- MQTT：`paho-mqtt`
- 透過 WebSocket 連 public broker

## 功能

- 使用 `invite_code` 作為聊天室 ID
- 訂閱 `chat/{invite_code}/#`
- 發送文字訊息到 `chat/{invite_code}/msg`
- 支援 JSON 訊息格式：
  - `{"type":"text","user":"Alice","content":"Hello"}`
  - `{"type":"image","user":"Alice","url":"https://example.com/image.jpg"}`
  - `{"type":"file","user":"Alice","filename":"demo.pdf","url":"https://example.com/demo.pdf"}`
- 圖片與檔案只傳 URL，不直接傳內容
- Enter 可發送訊息
- 即時顯示收到的 MQTT 訊息
- 氣泡會顯示訊息裡的 `user` 名字
- 自己送出的訊息顯示在右邊，其他人的訊息顯示在左邊

## 介面引導

App 開啟後會顯示三個使用步驟：

1. 填入房間代碼
2. 按「加入聊天室」
3. 開始傳訊息

同一組 `invite_code` 的使用者會進入同一間聊天室。

## 連線行為

App 會先連上固定的 broker，再訂閱聊天室 topic。只有訂閱成功後，輸入框才會啟用，避免還沒準備好就送訊息。

斷線時會自動重連同一個 broker，重連成功後會重新訂閱原本的聊天室。為了確保大家在同一間聊天室，App 不會自動切換到其他 broker。

同一間聊天室需要兩個條件都一樣：

- Broker WebSocket URL 一樣
- `invite_code` 一樣

## 執行

```bash
pip install -r requirements.txt
python app.py
```

預設 broker：

```text
wss://broker.emqx.io:8084/mqtt
```

如果預設 broker 真的無法連線，可以手動改成其他 public broker；但同聊天室的所有人也要一起改成同一個 URL：

```text
wss://broker.emqx.io:8084/mqtt
ws://broker.emqx.io:8083/mqtt
wss://broker.hivemq.com:8884/mqtt
ws://broker.hivemq.com:8000/mqtt
ws://broker.mqttdashboard.com:8000/mqtt
```

## Topic

同一個 `invite_code` 的使用者會在同一間聊天室：

```text
subscribe: chat/{invite_code}/#
publish:   chat/{invite_code}/msg
```

例如 invite code 是 `demo-room`：

```text
subscribe: chat/demo-room/#
publish:   chat/demo-room/msg
```

## 限制

- 不使用後端
- 不做帳號系統
- 不儲存歷史訊息
- Public broker 只適合 demo，不適合正式環境

## 檔案

- `app.py`：Python Qt6 聊天室主程式
- `requirements.txt`：需要安裝的 Python 套件
