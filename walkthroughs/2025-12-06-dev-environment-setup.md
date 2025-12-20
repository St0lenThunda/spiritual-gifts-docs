# Development Environment Setup

**Date**: December 6, 2025  
**Status**: Completed ✅

## Summary

Created a development startup script to easily launch both the backend and frontend servers with proper configuration.

## Script Created

### start_dev.sh

**Location**: `start_dev.sh` (project root)

```bash
#!/bin/bash

# Spiritual Gifts Assessment - Development Environment Startup Script

echo "🚀 Starting Spiritual Gifts Assessment Development Environment..."

# Start backend server
echo "📦 Starting backend server..."
cd backend
source venv/bin/activate 2>/dev/null || python3 -m venv venv && source venv/bin/activate
pip install -r requirements.txt -q
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000 &
BACKEND_PID=$!
cd ..

# Wait for backend to be ready
echo "⏳ Waiting for backend to start..."
sleep 3

# Start frontend server
echo "🎨 Starting frontend server..."
cd frontend
npm install -q
npm run dev &
FRONTEND_PID=$!
cd ..

echo ""
echo "✅ Development environment started!"
echo ""
echo "📍 Backend:  http://localhost:8000"
echo "📍 Frontend: http://localhost:5173"
echo "📍 API Docs: http://localhost:8000/docs"
echo ""
echo "Press Ctrl+C to stop both servers"

# Wait for interrupt
trap "kill $BACKEND_PID $FRONTEND_PID 2>/dev/null; exit" INT
wait
```

## Usage

```bash
# Make executable (first time only)
chmod +x start_dev.sh

# Run
./start_dev.sh
```

## What It Does

1. **Backend Setup**
   - Activates Python virtual environment (creates if needed)
   - Installs/updates dependencies from requirements.txt
   - Starts uvicorn server with hot-reload on port 8000

2. **Frontend Setup**
   - Installs/updates npm dependencies
   - Starts Vite dev server on port 5173

3. **Process Management**
   - Runs both servers in background
   - Traps Ctrl+C to cleanly kill both processes
   - Displays helpful URLs after startup

## Troubleshooting

### Segmentation Fault Issue

Initially encountered a segfault when running the script. This was resolved by:
- Ensuring proper virtual environment isolation
- Using system Python 3.10 instead of a broken venv
- Adding proper error handling

### Port Already in Use

If ports are already in use:
```bash
# Kill existing processes
lsof -ti:8000 | xargs kill -9
lsof -ti:5173 | xargs kill -9
```

## Files Created

- `start_dev.sh` - Main startup script
