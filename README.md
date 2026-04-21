# Messaging Platform Backend - MVP

A minimal real-time messaging platform backend built with **Node.js**, **TypeScript**, **Express**, **Pure WebSockets**, **Prisma ORM** and **PostgreSQL**.

## Description

**Messaging Platform Backend** is an MVP backend for a web-based chat application.

The project allows users to register, log in, search for other registered users, add them as contacts, create private conversations and exchange messages in real time using pure WebSockets.

Unlike Socket.io, this project uses native WebSocket communication through the `ws` package, giving more control over connection handling, message routing, online presence and typing indicators.

Messages are stored as encrypted content, meaning the backend is designed to receive, store and forward message content without needing to know the original plain text.

## MVP Features

- User registration
- User login
- JWT authentication
- Search registered users
- Add users as contacts
- List user contacts
- Remove contacts
- Create or get private conversations
- Get user conversations
- Send messages in real time
- Receive messages in real time
- Store encrypted messages
- Get conversation message history
- Online/offline user presence
- Typing indicator
- Prisma ORM database management

## Tech Stack

- Node.js
- TypeScript
- Express.js
- Pure WebSockets
- ws
- Prisma ORM
- PostgreSQL
- JWT
- Bcrypt

## Main MVP Flow

```txt
User registers
      ↓
User logs in
      ↓
User searches for another registered user
      ↓
User adds that user as a contact
      ↓
User creates or opens a private conversation
      ↓
User connects to the WebSocket server
      ↓
User sends a message
      ↓
Server stores the encrypted message
      ↓
Server forwards the message to the receiver in real time
```

## API Base URL

```txt
http://localhost:5000/api
```

## WebSocket URL

```txt
ws://localhost:5000/ws?token=jwt_token_here
```

The WebSocket connection requires a valid JWT token.

If the token is missing, invalid or expired, the server must close the connection.

## REST API Endpoints

## Authentication

| Method | Endpoint | Description |
|---|---|---|
| POST | `/auth/register` | Register a new user |
| POST | `/auth/login` | Login user and return JWT token |
| GET | `/auth/me` | Get authenticated user profile |

### Register User

```http
POST /api/auth/register
```

Request body:

```json
{
  "name": "Marlon Nhantumbo",
  "email": "marlon@example.com",
  "phoneNumber": "+258841234567",
  "password": "strongpassword"
}
```

Response:

```json
{
  "message": "User registered successfully",
  "user": {
    "id": "user_id",
    "name": "Marlon Nhantumbo",
    "email": "marlon@example.com",
    "phoneNumber": "+258841234567"
  }
}
```

### Login User

```http
POST /api/auth/login
```

Request body:

```json
{
  "email": "marlon@example.com",
  "password": "strongpassword"
}
```

Response:

```json
{
  "message": "Login successful",
  "token": "jwt_token_here",
  "user": {
    "id": "user_id",
    "name": "Marlon Nhantumbo",
    "email": "marlon@example.com",
    "phoneNumber": "+258841234567"
  }
}
```

### Get Authenticated User

```http
GET /api/auth/me
```

Headers:

```txt
Authorization: Bearer jwt_token_here
```

Response:

```json
{
  "id": "user_id",
  "name": "Marlon Nhantumbo",
  "email": "marlon@example.com",
  "phoneNumber": "+258841234567",
  "isOnline": true,
  "lastSeenAt": null
}
```

## Users

| Method | Endpoint | Description |
|---|---|---|
| GET | `/users/search?query=value` | Search registered users by name, email or phone number |

### Search Users

```http
GET /api/users/search?query=ashley
```

Headers:

```txt
Authorization: Bearer jwt_token_here
```

Response:

```json
[
  {
    "id": "user_id",
    "name": "Ashley Malate",
    "email": "ashley@example.com",
    "phoneNumber": "+258841112223",
    "isOnline": false
  }
]
```

## Contacts

Since this project is designed for a web application, contacts are managed inside the platform.

The backend does not access the user's device contacts. Users search for registered users and add them manually as contacts.

| Method | Endpoint | Description |
|---|---|---|
| POST | `/contacts` | Add a registered user as contact |
| GET | `/contacts` | Get authenticated user's contacts |
| DELETE | `/contacts/:contactId` | Remove a contact |

### Add Contact

```http
POST /api/contacts
```

Headers:

```txt
Authorization: Bearer jwt_token_here
```

Request body:

```json
{
  "contactUserId": "user_id_to_add"
}
```

Response:

```json
{
  "message": "Contact added successfully",
  "contact": {
    "id": "contact_id",
    "user": {
      "id": "user_id_to_add",
      "name": "Ashley Malate",
      "email": "ashley@example.com",
      "phoneNumber": "+258841112223"
    }
  }
}
```

### Get Contacts

```http
GET /api/contacts
```

Headers:

```txt
Authorization: Bearer jwt_token_here
```

Response:

```json
[
  {
    "id": "contact_id",
    "user": {
      "id": "user_id",
      "name": "Ashley Malate",
      "email": "ashley@example.com",
      "phoneNumber": "+258841112223",
      "isOnline": true,
      "lastSeenAt": null
    }
  }
]
```

### Remove Contact

```http
DELETE /api/contacts/contact_id
```

Headers:

```txt
Authorization: Bearer jwt_token_here
```

Response:

```json
{
  "message": "Contact removed successfully"
}
```

## Conversations

| Method | Endpoint | Description |
|---|---|---|
| POST | `/conversations` | Create or get a private conversation |
| GET | `/conversations` | Get authenticated user's conversations |
| GET | `/conversations/:conversationId/messages` | Get conversation message history |

### Create or Get Conversation

```http
POST /api/conversations
```

Headers:

```txt
Authorization: Bearer jwt_token_here
```

Request body:

```json
{
  "participantId": "receiver_user_id"
}
```

Response:

```json
{
  "id": "conversation_id",
  "participants": [
    {
      "id": "user_id_1",
      "name": "Marlon Nhantumbo"
    },
    {
      "id": "user_id_2",
      "name": "Ashley Malate"
    }
  ],
  "createdAt": "2026-04-21T10:00:00.000Z"
}
```

### Get Conversations

```http
GET /api/conversations
```

Headers:

```txt
Authorization: Bearer jwt_token_here
```

Response:

```json
[
  {
    "id": "conversation_id",
    "participants": [
      {
        "id": "user_id_2",
        "name": "Ashley Malate",
        "isOnline": true
      }
    ],
    "lastMessage": {
      "id": "message_id",
      "encryptedContent": "encrypted_message_here",
      "createdAt": "2026-04-21T10:00:00.000Z"
    },
    "updatedAt": "2026-04-21T10:00:00.000Z"
  }
]
```

### Get Conversation Messages

```http
GET /api/conversations/conversation_id/messages
```

Headers:

```txt
Authorization: Bearer jwt_token_here
```

Response:

```json
[
  {
    "id": "message_id",
    "conversationId": "conversation_id",
    "senderId": "user_id_1",
    "receiverId": "user_id_2",
    "encryptedContent": "encrypted_message_here",
    "status": "SENT",
    "createdAt": "2026-04-21T10:00:00.000Z"
  }
]
```

## WebSocket Event Format

Because this project uses pure WebSockets, every real-time message follows a custom JSON format:

```json
{
  "type": "event:name",
  "payload": {}
}
```

## MVP WebSocket Events

| Event | Direction | Description |
|---|---|---|
| `connection:ready` | Server to Client | Confirms successful WebSocket connection |
| `message:send` | Client to Server | Sends a message |
| `message:receive` | Server to Client | Delivers a message to the receiver |
| `typing:start` | Client to Server | User started typing |
| `typing:stop` | Client to Server | User stopped typing |
| `presence:online` | Server to Client | User became online |
| `presence:offline` | Server to Client | User became offline |
| `error` | Server to Client | Sends WebSocket error message |

## Example WebSocket Events

### Connection Ready

```json
{
  "type": "connection:ready",
  "payload": {
    "userId": "user_id",
    "message": "WebSocket connection established"
  }
}
```

### Send Message

```json
{
  "type": "message:send",
  "payload": {
    "conversationId": "conversation_id",
    "receiverId": "receiver_user_id",
    "encryptedContent": "encrypted_message_here"
  }
}
```

### Receive Message

```json
{
  "type": "message:receive",
  "payload": {
    "id": "message_id",
    "conversationId": "conversation_id",
    "senderId": "sender_user_id",
    "receiverId": "receiver_user_id",
    "encryptedContent": "encrypted_message_here",
    "status": "SENT",
    "createdAt": "2026-04-21T10:00:00.000Z"
  }
}
```

### Typing Start

```json
{
  "type": "typing:start",
  "payload": {
    "conversationId": "conversation_id",
    "receiverId": "receiver_user_id"
  }
}
```

### Typing Stop

```json
{
  "type": "typing:stop",
  "payload": {
    "conversationId": "conversation_id",
    "receiverId": "receiver_user_id"
  }
}
```

### User Online

```json
{
  "type": "presence:online",
  "payload": {
    "userId": "user_id"
  }
}
```

### User Offline

```json
{
  "type": "presence:offline",
  "payload": {
    "userId": "user_id",
    "lastSeenAt": "2026-04-21T10:05:00.000Z"
  }
}
```

### WebSocket Error

```json
{
  "type": "error",
  "payload": {
    "message": "Invalid event type"
  }
}
```

## Suggested Folder Structure

```txt
src/
├── config/
│   └── env.ts
│
├── controllers/
│   ├── auth.controller.ts
│   ├── user.controller.ts
│   ├── contact.controller.ts
│   └── conversation.controller.ts
│
├── services/
│   ├── auth.service.ts
│   ├── user.service.ts
│   ├── contact.service.ts
│   ├── conversation.service.ts
│   ├── message.service.ts
│   ├── presence.service.ts
│   └── websocket.service.ts
│
├── routes/
│   ├── auth.routes.ts
│   ├── user.routes.ts
│   ├── contact.routes.ts
│   └── conversation.routes.ts
│
├── middlewares/
│   ├── auth.middleware.ts
│   └── error.middleware.ts
│
├── websocket/
│   ├── websocket.server.ts
│   ├── websocket.router.ts
│   ├── websocket.types.ts
│   └── handlers/
│       ├── message.handler.ts
│       ├── typing.handler.ts
│       └── presence.handler.ts
│
├── prisma/
│   └── client.ts
│
├── utils/
│   ├── generateToken.ts
│   ├── hashPassword.ts
│   └── normalizePhoneNumber.ts
│
├── app.ts
└── server.ts
```

## MVP Prisma Schema

```prisma
model User {
  id           String    @id @default(uuid())
  name         String
  email        String    @unique
  phoneNumber  String    @unique
  passwordHash String
  isOnline     Boolean   @default(false)
  lastSeenAt   DateTime?
  createdAt    DateTime  @default(now())
  updatedAt    DateTime  @updatedAt

  contactsAsOwner Contact[] @relation("UserContacts")
  addedByUsers     Contact[] @relation("ContactUser")

  conversationParticipants ConversationParticipant[]

  sentMessages     Message[] @relation("SentMessages")
  receivedMessages Message[] @relation("ReceivedMessages")
}

model Contact {
  id            String   @id @default(uuid())
  ownerId       String
  contactUserId String
  createdAt     DateTime @default(now())

  owner       User @relation("UserContacts", fields: [ownerId], references: [id])
  contactUser User @relation("ContactUser", fields: [contactUserId], references: [id])

  @@unique([ownerId, contactUserId])
}

model Conversation {
  id        String   @id @default(uuid())
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  participants ConversationParticipant[]
  messages     Message[]
}

model ConversationParticipant {
  id             String @id @default(uuid())
  conversationId String
  userId         String

  conversation Conversation @relation(fields: [conversationId], references: [id])
  user         User         @relation(fields: [userId], references: [id])

  @@unique([conversationId, userId])
}

model Message {
  id               String        @id @default(uuid())
  conversationId   String
  senderId         String
  receiverId       String
  encryptedContent String
  status           MessageStatus @default(SENT)
  createdAt        DateTime      @default(now())

  conversation Conversation @relation(fields: [conversationId], references: [id])
  sender       User         @relation("SentMessages", fields: [senderId], references: [id])
  receiver     User         @relation("ReceivedMessages", fields: [receiverId], references: [id])
}

enum MessageStatus {
  SENT
  DELIVERED
  READ
}
```

## Installation

Clone the repository:

```bash
git clone https://github.com/your-username/messaging-platform-backend.git
```

Enter the project folder:

```bash
cd messaging-platform-backend
```

Install dependencies:

```bash
npm install
```

Install main packages:

```bash
npm install express ws cors dotenv jsonwebtoken bcryptjs @prisma/client
```

Install development dependencies:

```bash
npm install -D typescript ts-node-dev prisma @types/node @types/express @types/ws @types/cors @types/jsonwebtoken @types/bcryptjs
```

Initialize TypeScript:

```bash
npx tsc --init
```

Initialize Prisma:

```bash
npx prisma init
```

Create a `.env` file:

```env
PORT=5000
DATABASE_URL="postgresql://username:password@localhost:5432/messaging_platform"
JWT_SECRET="your_jwt_secret"
```

Run Prisma migration:

```bash
npx prisma migrate dev --name init
```

Generate Prisma Client:

```bash
npx prisma generate
```

Start the development server:

```bash
npm run dev
```

Open Prisma Studio:

```bash
npx prisma studio
```

## Example package.json Scripts

```json
{
  "scripts": {
    "dev": "ts-node-dev --respawn --transpile-only src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js",
    "prisma:migrate": "npx prisma migrate dev",
    "prisma:generate": "npx prisma generate",
    "prisma:studio": "npx prisma studio"
  }
}
```

## Environment Variables

| Variable | Description |
|---|---|
| `PORT` | Server port |
| `DATABASE_URL` | PostgreSQL database connection string |
| `JWT_SECRET` | Secret key used to sign JWT tokens |

## MVP Security Rules

- Passwords must be hashed before saving
- JWT is required for protected REST endpoints
- JWT is required for WebSocket connection
- The backend should only store encrypted message content
- Invalid WebSocket events should return an error
- Unauthorized WebSocket connections should be closed
- Users should only access conversations where they are participants
- Users should not be able to send messages in conversations they do not belong to

## Future Improvements

- Message delivery status
- Message read receipts
- Group conversations
- File and image messages
- Message reactions
- Message editing
- Delete message for everyone
- Offline message queue
- Push notifications
- Redis for horizontal scaling
- Refresh tokens
- Better end-to-end encryption key management
- Rate limiting for REST and WebSocket events

## What I Learned

This MVP explores core backend concepts such as authentication, WebSockets, real-time communication, custom event protocols, Prisma ORM, PostgreSQL, encrypted message storage, contact management and event-driven backend architecture.

Using pure WebSockets helped me understand how real-time systems work internally, including connection management, user socket mapping, message routing, typing indicators and online presence.

## Author

Developed by **Marlon Nhantumbo**.

## License

MIT License
