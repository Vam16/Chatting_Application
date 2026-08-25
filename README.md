# 💬 Chatting Application

### A real-time, WhatsApp-styled desktop chat app in Java — client & server over raw sockets

[![Java](https://img.shields.io/badge/Java-ED8B00?style=flat&logo=openjdk&logoColor=white)](#)
[![Swing](https://img.shields.io/badge/UI-Java%20Swing-blue?style=flat)](#)
[![Sockets](https://img.shields.io/badge/Networking-TCP%20Sockets-25D366?style=flat)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A two-window, real-time chat application built from scratch in Java Swing — no chat SDK, no framework, just raw `java.net.Socket`/`ServerSocket` and blocking I/O streams doing the messaging. The UI deliberately mirrors WhatsApp's look: a green header bar, rounded message bubbles, right-aligned outgoing messages, timestamps under every bubble.

## ✨ Features

- **Custom TCP protocol over raw sockets** — the server opens a `ServerSocket` on port `6001`; the client connects to `127.0.0.1:6001`. Messages are streamed with `DataInputStream`/`DataOutputStream.writeUTF()` — no external messaging library.
- **Two fully independent Swing windows** — `Server` and `Client` each run their own `JFrame`, own message history, and own send box; either side can run as a standalone application.
- **WhatsApp-style message bubbles** — `formatLabel()` renders each message as a rounded, colored `JLabel` with an HTML-wrapped body and a timestamp (`HH:mm`) stamped underneath, generated live with `Calendar`/`SimpleDateFormat`.
- **Live-updating conversation view** — incoming messages are appended to a `Box.createVerticalBox()` layout and the frame is revalidated on every new line, so the chat grows in real time without a manual refresh.
- **Branded chat header** — custom profile icon, name, "Active Now" status, and video/call icon placeholders laid out pixel-by-pixel with `setBounds()`, `Undecorated` frame for a borderless, app-like window.

## 🧱 Architecture

```
chatting_application/
├── Server.java                   # Listens on port 6001, own Swing window + message log
├── Client.java                   # Connects to 127.0.0.1:6001, own Swing window + message log
├── Chatting_Application.java     # NetBeans-generated main class (unused entry point)
└── icons/                        # UI assets — profile pictures, header icons, logo
```

`Server` and `Client` are two independent, symmetrical classes rather than a shared client/server abstraction — each owns its full UI and its own socket-handling `main()`. That keeps the demo simple to read end-to-end in one file per side, at the cost of some duplication between the two (both implement `formatLabel()` identically, for instance — a natural first refactor if this grows past a demo).

## 🛠️ Tech stack

| Layer | Technology |
|---|---|
| Language | Java |
| UI | Java Swing (hand-built layouts, no Designer/FXML) |
| Networking | Raw TCP sockets (`java.net.Socket`, `ServerSocket`) |
| I/O | `DataInputStream` / `DataOutputStream` (`writeUTF`/`readUTF`) |
| IDE project | NetBeans (Ant-based) |

## 🚀 Getting started

### Prerequisites

- **JDK 8+**
- **NetBeans** (or any IDE/CLI that can run a standard Ant-based Java project)

### Running it

The app is two separate programs that talk to each other over localhost — you run both:

1. **Clone the repository**
   ```bash
   git clone https://github.com/Vam16/Chatting_Application.git
   ```
2. **Start the server first** — run `Server.java`'s `main()`. Its window opens and it starts listening on port `6001`.
3. **Start the client** — run `Client.java`'s `main()` (from a second run configuration, or `java -cp build/classes chatting_application.Client` from a terminal). It connects to `127.0.0.1:6001` automatically.
4. **Chat** — type in either window's text box and hit **Send**. Messages appear instantly in both windows' conversation view, styled as chat bubbles with a timestamp.

> Note: `Chatting_Application.java` is the NetBeans-generated default main class and does nothing (`main()` is empty) — it is **not** the entry point. Run `Server` and `Client` directly.

## ⚠️ Known limitations

- **One client at a time** — `Server` blocks on `skt.accept()` inside its outer loop but never spins up a new thread per connection, so a second client connecting concurrently isn't handled.
- **Hard-coded host/port** (`127.0.0.1:6001`) — fine for a local demo, would need to become configurable for anything beyond localhost.
- **No reconnect/error handling on disconnect** — if either side closes, the other's read loop throws and the stack trace prints to console instead of failing gracefully.

These are natural next steps rather than defects in what's there — the core real-time messaging over a hand-rolled protocol works, which is the actual point of the exercise.

## 📝 License

Distributed under the MIT License. See [`LICENSE`](LICENSE) for details.

## 👤 Author

**Mihai-Andrei Vasile**
