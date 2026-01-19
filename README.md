# Chat Application Backend

A Node.js + Express REST API for a chat application with TypeScript and MongoDB.

## Features

- **Chat Session Management**: Create, read, update, archive, and delete chat sessions
- **Message Handling**: Send messages to AI and store responses
- **User Settings**: Manage user preferences and privacy settings
- **History Management**: Automatic and manual chat history control
- **Text-to-Speech**: Convert text to natural-sounding audio
- **MongoDB Integration**: Persistent storage with Mongoose ODM
- **TypeScript**: Full type safety and better development experience
- **Environment Variables**: Secure configuration management
- **Health Check**: Server status monitoring

## API Endpoints

### Chat Sessions
- `POST /api/chats` - Create a new chat session
- `GET /api/chats` - Get all chat sessions (supports `?archived=true/false` filter, requires `userId`)
- `GET /api/chats/:id` - Get a single chat session with all messages
- `PUT /api/chats/:id` - Update chat session (title, archived status)
- `PATCH /api/chats/:id/archive` - Archive a chat session
- `DELETE /api/chats/:id` - Delete a chat session

### Messages
- `POST /api/chats/:id/messages` - Send a message to AI and store response (requires `userId`)

### User Settings
- `GET /api/users/:userId/settings` - Get user settings
- `PUT /api/users/:userId/settings` - Update user settings
- `DELETE /api/users/:userId/history` - Clear all chat history for user
- `GET /api/users/:userId/history/stats` - Get chat history statistics

### Text-to-Speech (TTS)
- `POST /api/tts/generate` - Generate speech and return audio URL
- `POST /api/tts/generate-buffer` - Generate speech and return audio buffer
- `GET /api/audio/:filename` - Serve audio file
- `DELETE /api/audio/:filename` - Delete audio file
- `GET /api/tts/voices` - Get available voices
- `GET /api/tts/languages` - Get supported languages

## User Settings Options

```json
{
  "saveChatHistory": boolean,
  "autoDeleteHistory": boolean,
  "autoDeleteDays": number (1-365),
  "theme": "light" | "dark" | "system",
  "language": string,
  "notifications": boolean
}
```

## History Management

### Server-Side Enforcement
- **Chat History Toggle**: When `saveChatHistory` is false, no new chats or messages are saved
- **Auto-Delete**: When enabled, automatically deletes chats older than specified days
- **Immediate Cleanup**: History rules are enforced after each message and settings update

### History Features
- **Per-User Isolation**: All chat data is isolated by `userId`
- **Automatic Cleanup**: Old chats are automatically deleted based on user settings
- **Manual Clear**: Users can clear all their history at once
- **Statistics**: Track total chats, messages, and storage metrics

## Setup

### Prerequisites
- Node.js (v16 or higher)
- MongoDB
- npm or yarn

### Installation

1. Clone the repository
2. Install dependencies:
   ```bash
   npm install
   ```

3. Create environment variables file:
   ```bash
   cp .env.example .env
   ```

4. Update `.env` with your configuration:
   ```
   PORT=3000
   MONGODB_URI=mongodb://localhost:27017/chat-app
   NODE_ENV=development
   AI_API_KEY=your_openai_api_key_here
   AI_API_URL=https://api.openai.com/v1/chat/completions
   ```

### Running the Application

**Development mode:**
```bash
npm run dev
```

**Production mode:**
```bash
npm run build
npm start
```

## Project Structure

```
src/
├── config/
│   └── database.ts      # MongoDB connection setup
├── controllers/
│   ├── chatController.ts # Chat session logic
│   ├── userController.ts # User settings logic
│   └── ttsController.ts  # Text-to-speech logic
├── models/
│   ├── ChatSession.ts   # Chat session schema
│   ├── Message.ts       # Message schema
│   └── User.ts          # User settings schema
├── routes/
│   ├── chatRoutes.ts    # Chat API routes
│   ├── userRoutes.ts    # User API routes
│   └── ttsRoutes.ts     # TTS API routes
├── services/
│   ├── aiService.ts     # AI integration service
│   ├── historyService.ts # History management service
│   └── ttsService.ts    # Text-to-speech service
├── types/
│   └── index.ts         # TypeScript type definitions
└── index.ts             # Server entry point
```

## Usage Examples

### Create a new chat session
```bash
curl -X POST http://localhost:3000/api/chats \
  -H "Content-Type: application/json" \
  -d '{"title": "My New Chat", "userId": "user123"}'
```

### Send a message
```bash
curl -X POST http://localhost:3000/api/chats/[chat-id]/messages \
  -H "Content-Type: application/json" \
  -d '{"content": "Hello, how are you?", "userId": "user123"}'
```

### Get user settings
```bash
curl http://localhost:3000/api/users/user123/settings
```

### Update user settings
```bash
curl -X PUT http://localhost:3000/api/users/user123/settings \
  -H "Content-Type: application/json" \
  -d '{"saveChatHistory": false, "autoDeleteHistory": true, "autoDeleteDays": 7}'
```

### Clear all history
```bash
curl -X DELETE http://localhost:3000/api/users/user123/history
```

### Get history statistics
```bash
curl http://localhost:3000/api/users/user123/history/stats
```

### Get all chats for a user
```bash
curl "http://localhost:3000/api/chats?userId=user123"
```

### Generate speech (returns audio URL)
```bash
curl -X POST http://localhost:3000/api/tts/generate \
  -H "Content-Type: application/json" \
  -d '{"text": "Hello, world!", "voice": "alloy", "speed": 1.0}'
```

### Generate speech (returns audio buffer)
```bash
curl -X POST http://localhost:3000/api/tts/generate-buffer \
  -H "Content-Type: application/json" \
  -d '{"text": "Hello, world!", "voice": "alloy"}' \
  --output audio.mp3
```

### Get available voices
```bash
curl http://localhost:3000/api/tts/voices
```

### Get supported languages
```bash
curl http://localhost:3000/api/tts/languages
```

## Environment Variables

- `PORT`: Server port (default: 3000)
- `MONGODB_URI`: MongoDB connection string
- `NODE_ENV`: Environment (development/production)
- `AI_API_KEY`: OpenAI API key for AI responses
- `AI_API_URL`: AI API endpoint URL
- `TTS_API_KEY`: OpenAI API key for Text-to-Speech
- `TTS_API_URL`: TTS API endpoint URL
- `AUDIO_STORAGE_PATH`: Path to store generated audio files (default: ./audio)

## Error Handling

All API responses follow a consistent format:

**Success:**
```json
{
  "success": true,
  "data": { ... }
}
```

**Error:**
```json
{
  "success": false,
  "error": "Error message"
}
```

## Security & Privacy

- **Server-Side Enforcement**: All history rules are enforced server-side
- **User Isolation**: Data is strictly separated by `userId`
- **Privacy Controls**: Users can disable history saving entirely
- **Automatic Cleanup**: Configurable auto-deletion prevents data accumulation
- **No API Keys in Code**: All secrets read from environment variables

## Development

The project uses TypeScript with strict type checking. All errors are handled gracefully with proper HTTP status codes.

## License

ISC
