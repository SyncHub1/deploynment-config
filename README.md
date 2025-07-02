# 🚀 Real-Time Media Processing System

A highly scalable, real-time communication architecture for processing images, videos, and audio files using WebSockets, Redis Pub/Sub, message queueing, and Cloudinary storage.

## 📋 Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [API Documentation](#api-documentation)
- [Deployment](#deployment)
- [Troubleshooting](#troubleshooting)

## 🎯 Overview

This system provides real-time media processing capabilities with:

- **Asynchronous Processing**: Files are processed in the background using worker queues
- **Real-Time Updates**: WebSocket connections provide live progress updates
- **Scalable Architecture**: Horizontal scaling support with Redis and message queues
- **Cloud Storage**: Processed files stored in Cloudinary with CDN delivery
- **Multiple Formats**: Support for images, videos, and audio processing

## 🏗️ Architecture

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Frontend  │    │  API Service│    │WebSocket Svc│
│   (React)   │◄──►│   (Express) │◄──►│ (Socket.IO) │
└─────────────┘    └─────────────┘    └─────────────┘
                          │                    │
                          ▼                    ▼
                   ┌─────────────┐    ┌─────────────┐
                   │   MongoDB   │    │    Redis    │
                   │ (Metadata)  │    │ (Pub/Sub)   │
                   └─────────────┘    └─────────────┘
                          │                    │
                          ▼                    ▼
                   ┌─────────────┐    ┌─────────────┐
                   │   BullMQ    │    │   Workers   │
                   │  (Queues)   │◄──►│(Sharp/FFmpeg)│
                   └─────────────┘    └─────────────┘
                                              │
                                              ▼
                                     ┌─────────────┐
                                     │  Cloudinary │
                                     │ (Storage)   │
                                     └─────────────┘
```

## ✨ Features

### 🖼️ Image Processing
- Resize and crop images
- Format conversion (JPEG, PNG, WebP)
- Quality optimization
- Thumbnail generation
- Metadata extraction

### 🎬 Video Processing
- Multiple resolution outputs (480p, 720p, 1080p)
- Format transcoding
- Audio extraction
- Thumbnail generation
- Duration and metadata extraction

### 🎵 Audio Processing
- Format conversion (MP3, OGG, AAC)
- Quality optimization
- Duration extraction

### 🔄 Real-Time Updates
- Live progress tracking
- Status notifications
- Error handling
- Connection management

## 🛠️ Tech Stack

| Component | Technology |
|-----------|------------|
| **Frontend** | React, Socket.IO Client |
| **API Service** | Node.js, Express, Multer |
| **WebSocket Service** | Socket.IO, Redis Adapter |
| **Message Queue** | BullMQ, Redis |
| **Database** | MongoDB, Mongoose |
| **Image Processing** | Sharp |
| **Media Processing** | FFmpeg, fluent-ffmpeg |
| **Storage** | Cloudinary |
| **Cache/Pub-Sub** | Redis |
| **UI Components** | Tailwind CSS, shadcn/ui |

## 📦 Installation

### Prerequisites

- Node.js 18+ 
- Redis 6+
- MongoDB 5+
- FFmpeg (for media processing)

### 1. Clone and Setup

```bash
# Clone the repository
git clone <your-repo-url>
cd media-processing-system

# Install dependencies for all services
cd shared-utils && npm install
cd ../media-api-service && npm install
cd ../media-workers && npm install
cd ../websocket-service && npm install
cd ../frontend && npm install
```

### 2. Install FFmpeg

**macOS:**
```bash
brew install ffmpeg
```

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install ffmpeg
```

**Windows:**
Download from [FFmpeg official website](https://ffmpeg.org/download.html)

### 3. Setup Environment

```bash
# Copy environment template
cp env.example .env

# Edit .env with your configuration
nano .env
```

### 4. Start Services

```bash
# Terminal 1: Start Redis
redis-server

# Terminal 2: Start MongoDB
mongod

# Terminal 3: Start API Service
cd media-api-service
npm run dev

# Terminal 4: Start WebSocket Service
cd websocket-service
npm run dev

# Terminal 5: Start Workers
cd media-workers
npm run start:image  # Image processor
npm run start:media  # Media processor

# Terminal 6: Start Frontend
cd frontend
npm run dev
```

## ⚙️ Configuration

### Environment Variables

Copy `env.example` to `.env` and configure:

```bash
# Redis
REDIS_URL=redis://localhost:6379

# MongoDB
MONGODB_URI=mongodb://localhost:27017/media-processing

# Cloudinary
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret

# Service Ports
API_PORT=3001
WEBSOCKET_PORT=3002

# Frontend
FRONTEND_URL=http://localhost:3000
```

### Cloudinary Setup

1. Create a [Cloudinary account](https://cloudinary.com/)
2. Get your credentials from the dashboard
3. Update the environment variables

## 🚀 Usage

### Frontend Integration

```jsx
import MediaUpload from './Components/MediaUpload/MediaUpload';
import useWebSocket from './hooks/useWebSocket';

function App() {
  const userId = 'user123';
  const { isConnected, processingUpdates } = useWebSocket(userId);

  const handleUploadComplete = (data) => {
    console.log('Upload completed:', data);
  };

  return (
    <div>
      <MediaUpload 
        userId={userId} 
        onUploadComplete={handleUploadComplete}
      />
    </div>
  );
}
```

### API Usage

```javascript
import { submitMedia, getSubmissionStatus } from './api/mediaApi';

// Submit a file for processing
const response = await submitMedia(file, {
  userId: 'user123',
  title: 'My Image',
  description: 'A beautiful image',
  tags: ['nature', 'landscape'],
  resize: { width: 1920, height: 1080 },
  format: 'webp',
  quality: 80,
  generateThumbnail: true
});

// Get processing status
const status = await getSubmissionStatus(submissionId, userId);
```

## 📚 API Documentation

### Endpoints

#### POST `/api/submissions/submit`
Upload and process a media file.

**Request:**
```javascript
const formData = new FormData();
formData.append('file', file);
formData.append('userId', 'user123');
formData.append('title', 'My Media');
formData.append('description', 'Description');
formData.append('tags', 'tag1,tag2');
formData.append('resize', JSON.stringify({ width: 1920, height: 1080 }));
formData.append('format', 'webp');
formData.append('quality', '80');
formData.append('generateThumbnail', 'true');
```

**Response:**
```json
{
  "success": true,
  "message": "File uploaded and queued for processing",
  "data": {
    "submissionId": "64f1a2b3c4d5e6f7g8h9i0j1",
    "jobId": "job-123",
    "status": "pending",
    "progress": 0
  }
}
```

#### GET `/api/submissions/status/:submissionId`
Get processing status for a submission.

#### GET `/api/submissions/user/:userId`
Get all submissions for a user.

#### DELETE `/api/submissions/:submissionId`
Delete a submission.

### WebSocket Events

#### Client to Server
- `authenticate` - Authenticate user
- `subscribe` - Subscribe to job updates
- `unsubscribe` - Unsubscribe from job updates
- `ping` - Health check

#### Server to Client
- `authenticated` - Authentication response
- `processing-update` - Processing status update
- `notification` - General notification
- `pong` - Health check response

## 🚀 Deployment

### Docker Deployment

```dockerfile
# Example Dockerfile for API service
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
EXPOSE 3001
CMD ["npm", "start"]
```

### Environment Setup

1. **Production Redis**: Use Redis Cloud or AWS ElastiCache
2. **Production MongoDB**: Use MongoDB Atlas or AWS DocumentDB
3. **Load Balancer**: Use nginx or AWS ALB for multiple instances
4. **Monitoring**: Use PM2 or Docker for process management

### Scaling

- **Horizontal Scaling**: Run multiple instances of each service
- **Load Balancing**: Use Redis for session sharing
- **Database**: Use MongoDB replica sets
- **Storage**: Cloudinary handles CDN and scaling automatically

## 🔧 Troubleshooting

### Common Issues

1. **FFmpeg not found**
   ```bash
   # Install FFmpeg
   brew install ffmpeg  # macOS
   sudo apt install ffmpeg  # Ubuntu
   ```

2. **Redis connection failed**
   ```bash
   # Check Redis is running
   redis-cli ping
   # Should return PONG
   ```

3. **MongoDB connection failed**
   ```bash
   # Check MongoDB is running
   mongo --eval "db.runCommand('ping')"
   ```

4. **Cloudinary upload failed**
   - Verify API credentials in `.env`
   - Check Cloudinary account status
   - Verify file size limits

### Logs

Check service logs for detailed error information:

```bash
# API Service logs
cd media-api-service && npm run dev

# Worker logs
cd media-workers && npm run start:image

# WebSocket logs
cd websocket-service && npm run dev
```

### Performance Tuning

1. **Worker Concurrency**: Adjust in worker files
2. **Queue Settings**: Modify BullMQ configuration
3. **File Limits**: Update multer configuration
4. **Memory Usage**: Monitor with `htop` or `top`

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🆘 Support

For support and questions:

- Create an issue in the repository
- Check the troubleshooting section
- Review the API documentation

---

**Happy Processing! 🎉** 