# Zoom Clone - Real-Time Video Conferencing Web Application

A full-stack, real-time video conferencing web application inspired by Zoom, built with **React**, **Node.js**, **Express**, **Socket.IO**, and **WebRTC**. It enables users to create and join virtual meeting rooms instantly, communicate via high-definition video and low-latency audio, share screens, and exchange real-time chat messages.

---

## Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | React 19, React Router v7, Material UI (MUI), Axios, Socket.IO Client |
| **Backend** | Node.js, Express.js (ES Modules), Socket.IO, CORS |
| **Database** | MongoDB with Mongoose |
| **Real-Time Communication** | WebRTC (Mesh Architecture), Google STUN (`stun.l.google.com:19302`) |
| **Security & Auth** | bcrypt password hashing, Crypto session tokens |

---

## Key Features

- **User Authentication**: Secure user registration and login with bcrypt password hashing and token management.
- **Instant Meeting Rooms**: Create custom meeting rooms or join with a unique meeting code.
- **HD Video & Low-Latency Audio**: Peer-to-peer real-time media streaming powered by WebRTC.
- **Audio & Video Controls**: Toggle camera on/off and microphone mute/unmute at any point during a call.
- **Screen Sharing**: One-click screen sharing for presentations and collaborative workflows.
- **Real-Time Chat**: In-meeting text chat with instant delivery and unread message indicators.
- **Pre-Meeting Lobby**: Verify camera/microphone permissions and preview video before entering a room.
- **Meeting History**: Track and review past meeting participation linked to your user account.
- **Responsive Interface**: Clean, modern dark-themed user interface built with Material UI.

---

## Architecture & Communication Flow

The application combines client-server REST APIs with peer-to-peer WebRTC connections managed via Socket.IO signaling.

```text
               +-----------------------------------+
               |          Node.js / Express        |
               |             (Port 8000)           |
               +-----------------+-----------------+
                                 |
                 REST API (Auth) | Socket.IO (Signaling)
                                 |
             +-------------------+-------------------+
             |                                       |
             v                                       v
     +---------------+                       +---------------+
     |  Participant  |<====== WebRTC =======>|  Participant  |
     |   Client A    |      Peer-to-Peer     |   Client B    |
     +---------------+ (Audio, Video, Screen)+---------------+
```

1. **Authentication**: Users sign up and log in via Express REST endpoints; tokens are issued and saved locally.
2. **Room Coordination**: Socket.IO connects participants to room channels (`join-call`, `user-joined`, `user-left`).
3. **WebRTC Signaling**: SDP offers, answers, and ICE candidates are exchanged between peers via Socket.IO (`signal`).
4. **Media Streaming**: Direct peer-to-peer media streams are established for video, audio, and screen sharing.
5. **Meeting Logging**: Meeting participation codes are saved to MongoDB under the user's account.

---

## Project Structure

```text
Zoom/
├── Backend/
│   ├── src/
│   │   ├── controllers/
│   │   │   ├── socketManager.js       # WebRTC signaling & real-time chat socket handlers
│   │   │   └── user.controller.js     # Auth & meeting history controllers
│   │   ├── models/
│   │   │   ├── meeting.model.js       # Meeting schema
│   │   │   └── user.model.js          # User schema
│   │   ├── routes/
│   │   │   └── users.routes.js        # User & meeting routes
│   │   └── app.js                     # Express server & socket initialization
│   ├── package.json
│   └── package-lock.json
├── frontend/
│   ├── public/                        # Static assets and index.html
│   ├── src/
│   │   ├── contexts/
│   │   │   └── AuthContext.jsx        # Authentication state management
│   │   ├── pages/
│   │   │   ├── authentication.jsx     # Login & Signup views
│   │   │   ├── history.jsx            # Meeting history view
│   │   │   ├── home.jsx               # Dashboard to join/create meetings
│   │   │   ├── landing.jsx            # Landing page
│   │   │   └── VideoMeet.jsx          # Video conference room & WebRTC logic
│   │   ├── styles/                    # Component CSS modules
│   │   ├── utils/
│   │   │   └── withAuth.jsx           # Protected route HOC
│   │   ├── App.js                     # App router
│   │   ├── environment.js             # API server configuration
│   │   └── index.js                   # React entry point
│   ├── package.json
│   └── package-lock.json
├── .gitignore
└── README.md
```

---

## Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) (v18 or higher recommended)
- [npm](https://www.npmjs.com/) (v9 or higher)
- [MongoDB](https://www.mongodb.com/) (local instance or MongoDB Atlas cluster)

---

### Installation & Setup

#### 1. Clone the repository

```bash
git clone https://github.com/pradeep7-ai/zoom.git
cd zoom
```

#### 2. Backend Setup

```bash
cd Backend
npm install
```

Configure environment variables in a `.env` file (or update `Backend/src/app.js`):

```env
PORT=8000
MONGO_URI=your_mongodb_connection_string
```

Start the backend server:

```bash
# Development (with nodemon)
npm run dev

# Production
npm start
```

The backend server will run on `http://localhost:8000`.

---

#### 3. Frontend Setup

In a separate terminal window:

```bash
cd frontend
npm install
```

Update `frontend/src/environment.js` with your backend server URL:

```javascript
const server = {
    dev: "http://localhost:8000",
    prod: "http://localhost:8000" // or your deployed production backend URL
}
export default server;
```

Start the React development server:

```bash
npm start
```

The frontend will open in your browser at `http://localhost:3000`.

---

## API Reference

### User Routes (`/api/v1/users`)

| Method | Endpoint | Description | Request Body / Params |
|---|---|---|---|
| `POST` | `/register` | Register a new user | `{ name, username, password }` |
| `POST` | `/login` | Authenticate user and obtain token | `{ username, password }` |
| `POST` | `/add_to_activity` | Add meeting code to user history | `{ token, meeting_code }` |
| `GET` | `/get_all_activity` | Get user meeting history | Query param: `?token=<token>` |

---

## Socket.IO Events

| Event | Direction | Payload | Description |
|---|---|---|---|
| `join-call` | Client -> Server | `path` (meeting code) | Join a meeting room |
| `user-joined` | Server -> Client | `socketId, connections` | Notifies room members of a new participant |
| `signal` | Bi-directional | `toId, message` | WebRTC SDP offer, answer, and ICE candidate exchange |
| `chat-message` | Bi-directional | `data, sender, socketId` | Broadcasts real-time messages within the meeting room |
| `user-left` | Server -> Client | `socketId` | Notifies remaining participants when someone disconnects |
| `disconnect` | Client -> Server | - | Cleans up socket connections and active room lists |

---

## License

This project is licensed under the [ISC License](LICENSE).
