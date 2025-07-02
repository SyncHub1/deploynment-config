# 🚀 Deployment Guide for Render

This guide will help you deploy the real-time media processing system on Render.

## 📋 Prerequisites

1. **Render Account**: Sign up at [render.com](https://render.com)
2. **Cloudinary Account**: Sign up at [cloudinary.com](https://cloudinary.com)
3. **GitHub Repository**: Push your code to GitHub

## 🔧 Setup Steps

### Step 1: Prepare Your Repository

Ensure your repository structure looks like this:
```
your-repo/
├── media-api-service/
├── websocket-service/
├── media-workers/
├── shared-utils/
├── frontend/
└── README.md
```

### Step 2: Configure Cloudinary

1. Go to your [Cloudinary Dashboard](https://cloudinary.com/console)
2. Copy your credentials:
   - Cloud Name
   - API Key
   - API Secret

### Step 3: Deploy Services on Render

#### Option A: Using Render Dashboard (Recommended)

1. **Deploy API Service**:
   - Go to Render Dashboard
   - Click "New" → "Web Service"
   - Connect your GitHub repository
   - Set root directory to `media-api-service`
   - Build Command: `npm install`
   - Start Command: `npm start`
   - Add environment variables:
     ```
     NODE_ENV=production
     REDIS_URL=<will be set automatically>
     MONGODB_URI=<will be set automatically>
     CLOUDINARY_CLOUD_NAME=your_cloud_name
     CLOUDINARY_API_KEY=your_api_key
     CLOUDINARY_API_SECRET=your_api_secret
     FRONTEND_URL=https://your-frontend-app.onrender.com
     ```

2. **Deploy WebSocket Service**:
   - Click "New" → "Web Service"
   - Connect your GitHub repository
   - Set root directory to `websocket-service`
   - Build Command: `npm install`
   - Start Command: `npm start`
   - Add environment variables:
     ```
     NODE_ENV=production
     REDIS_URL=<will be set automatically>
     FRONTEND_URL=https://your-frontend-app.onrender.com
     WEBSOCKET_PORT=10000
     ```

3. **Deploy Worker Services**:
   - Click "New" → "Background Worker"
   - Connect your GitHub repository
   - Set root directory to `media-workers`
   - Build Command: `npm install`
   - Start Command: `npm run start:image` (for image processor)
   - Repeat for media processor with `npm run start:media`

4. **Create Redis Database**:
   - Click "New" → "Redis"
   - Choose a name (e.g., `media-redis`)
   - Select your plan

5. **Create MongoDB Database**:
   - Click "New" → "PostgreSQL" (Render doesn't have MongoDB, use PostgreSQL)
   - Choose a name (e.g., `media-db`)
   - Select your plan

#### Option B: Using render.yaml (Advanced)

1. Create a single `render.yaml` file in your root directory
2. Push to GitHub
3. Render will automatically detect and deploy all services

### Step 4: Update Frontend Environment Variables

Update your frontend's environment variables:

```bash
# .env.production
REACT_APP_API_URL=https://your-media-api-service.onrender.com
REACT_APP_WEBSOCKET_URL=https://your-websocket-service.onrender.com
```

### Step 5: Deploy Frontend

1. Go to Render Dashboard
2. Click "New" → "Static Site"
3. Connect your GitHub repository
4. Set root directory to `frontend`
5. Build Command: `npm run build`
6. Publish Directory: `dist` (or `build` for Create React App)

## 🔗 Service URLs

After deployment, you'll have these URLs:

- **Frontend**: `https://your-frontend-app.onrender.com`
- **API Service**: `https://your-media-api-service.onrender.com`
- **WebSocket Service**: `https://your-websocket-service.onrender.com`
- **Redis**: `redis://your-redis-instance.onrender.com:6379`
- **Database**: `postgresql://your-db-instance.onrender.com`

## 🔧 Environment Variables

### Required for All Services
```bash
NODE_ENV=production
REDIS_URL=redis://your-redis-instance.onrender.com:6379
```

### API Service & Workers
```bash
MONGODB_URI=postgresql://your-db-instance.onrender.com
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
FRONTEND_URL=https://your-frontend-app.onrender.com
```

### WebSocket Service
```bash
FRONTEND_URL=https://your-frontend-app.onrender.com
WEBSOCKET_PORT=10000
```

## 🐛 Troubleshooting

### Common Issues

1. **Build Failures**:
   - Check if all dependencies are in `package.json`
   - Ensure Node.js version is compatible (18+)

2. **Connection Errors**:
   - Verify environment variables are set correctly
   - Check if services are running

3. **FFmpeg Issues**:
   - Render doesn't support FFmpeg in web services
   - Use worker services for media processing
   - Consider using external FFmpeg services

### Health Checks

Test your services:

```bash
# API Service
curl https://your-media-api-service.onrender.com/health

# WebSocket Service
curl https://your-websocket-service.onrender.com/health
```

## 📊 Monitoring

1. **Render Dashboard**: Monitor service health and logs
2. **Cloudinary Dashboard**: Check uploads and processing
3. **Redis Dashboard**: Monitor queue and cache performance

## 🔄 Scaling

### Horizontal Scaling
- Render automatically scales web services
- Worker services can be scaled manually
- Redis handles session sharing

### Performance Optimization
- Use CDN for static assets
- Enable Redis caching
- Optimize image/video processing settings

## 💰 Cost Optimization

1. **Free Tier Limits**:
   - 750 hours/month for web services
   - 750 hours/month for workers
   - 1GB Redis storage
   - 1GB PostgreSQL storage

2. **Upgrade When Needed**:
   - Monitor usage in Render dashboard
   - Upgrade services based on traffic

## 🔒 Security

1. **Environment Variables**: Never commit secrets to Git
2. **CORS**: Configure allowed origins properly
3. **Rate Limiting**: Implement in API service
4. **Authentication**: Use your existing auth system

## 📝 Next Steps

1. **Test the Integration**: Upload images and videos
2. **Monitor Performance**: Check processing times
3. **Scale as Needed**: Upgrade services based on usage
4. **Add Features**: Implement additional processing options

---

**Need Help?** Check Render's documentation or create an issue in your repository. 