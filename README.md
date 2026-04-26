# Email_Simulation_System
# MailBox — Email_Simulation_System_in_java

A Java-based client-server email simulation application with a modern Swing GUI. Users can register, log in, compose and send emails, manage their inbox/outbox, and exchange file attachments — all over a local TCP connection.

---

## Features

- **User Authentication** — Register new accounts and log in with a username/password
- **Compose & Send** — Write emails with subject, body, and optional file attachment
- **Inbox & Outbox** — View received and sent messages in sortable tables
- **Attachments** — Upload files when sending; download them from received emails
- **Edit Sent Emails** — Modify the subject and body of outgoing messages
- **Delete Emails** — Remove emails from your own view (inbox or outbox) without affecting the other party
- **Search & Filter** — Filter emails by keyword in real time
- **Star / Favourite** — Mark important emails with a star
- **Reply** — One-click reply pre-fills the recipient and subject
- **Statistics Panel** — At-a-glance counts for sent, received, and starred emails
- **Persistent Storage** — Server state is saved to `server_data.ser` and restored on restart
- **Multi-client Support** — Thread-pool-based server handles multiple simultaneous clients

---

## Project Structure

```
Project/
├── src/
│   └── Email/
│       ├── EmailServer.java      # TCP server, user store, command handlers
│       └── EmailClientGUI.java   # Swing GUI client
├── server_attachments/           # Uploaded files stored here at runtime
├── server_data.ser               # Serialised user/email data (auto-created)
├── nbproject/                    # NetBeans project metadata
├── build.xml                     # Ant build script
└── manifest.mf
```

---

## Prerequisites

| Requirement | Version |
|-------------|---------|
| Java JDK    | 8 or later |
| NetBeans IDE | 12+ (optional, for IDE workflow) |

---

## Running the Application

### 1. Compile

**With NetBeans:** Open the `Project` folder as a NetBeans project and click **Run → Build Project**.

**With Ant (command line):**
```bash
cd Project
ant jar
```

**With javac directly:**
```bash
javac -d out src/Email/EmailServer.java src/Email/EmailClientGUI.java
```

### 2. Start the Server

```bash
java -cp out email.EmailServer
```

The server listens on **port 5000**. You should see:
```
Email Server started on port 5000
=====================================
```

### 3. Launch the Client

Open a new terminal (repeat for multiple clients):
```bash
java -cp out email.EmailClientGUI
```

The client connects to `localhost:5000` automatically on startup.

---

## Default Demo Accounts

Three accounts are pre-loaded for quick testing:

| Username  | Password     |
|-----------|--------------|
| `alice`   | `alice123`   |
| `bob`     | `bob123`     |
| `charlie` | `charlie123` |

You can also register new accounts from the login screen.

---

## How It Works

### Architecture

```
EmailClientGUI  ──TCP:5000──►  EmailServer
  (Swing GUI)                  (Multi-threaded)
```

Communication uses Java **Object Serialization** over a raw TCP socket. The client sends a command string followed by any required arguments; the server responds with a status (`SUCCESS` / `FAILED`) and a message string. Binary attachment data is streamed as raw bytes after the object headers.

### Supported Commands

| Command                | Description                              |
|------------------------|------------------------------------------|
| `REGISTER`             | Create a new user account                |
| `LOGIN`                | Authenticate an existing user            |
| `SEND_EMAIL`           | Send an email to another user            |
| `GET_INBOX`            | Retrieve the current user's inbox        |
| `GET_OUTBOX`           | Retrieve the current user's outbox       |
| `DELETE_EMAIL`         | Mark an email deleted in inbox or outbox |
| `DELETE_FOR_ME`        | Remove email from the current user's view only |
| `EDIT_EMAIL`           | Update subject/body of a sent email      |
| `UPLOAD_ATTACHMENT`    | Stream a file to the server              |
| `DOWNLOAD_ATTACHMENT`  | Retrieve an attachment from the server   |
| `LOGOUT`               | End the session                          |

### Data Persistence

User accounts and email data are serialised to `server_data.ser` in the working directory after every write operation. On startup, the server loads this file and merges saved users with the defaults.

Attachment files are stored in the `server_attachments/` directory with the naming convention:
```
<username>_<timestamp>_<originalFilename>
```

---

## Notes

- The server and client must run on the same machine (or you must update the hostname in `EmailClientGUI` from `localhost` to the server's IP).
- Passwords are stored in plain text — this is a simulation project, not production-ready software.
- Email objects are shared by reference between sender outbox and receiver inbox; editing a sent email is therefore reflected on both sides.
