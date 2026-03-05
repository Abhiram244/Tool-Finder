# Tool Finder - Smart Tool Recommendation Platform

## Project Overview
This is just the MVP not the final product
Tool Finder is an AI-powered platform that provides personalized software tool recommendations based on specific user requirements. It currently Google's Gemini 2.5 Flash API with search grounding to suggestions but that will change in the final product

## Problems Solved

- **Tool Discovery Fatigue**: Eliminates the overwhelming process of researching tools manually
- **Decision Paralysis**: Provides clear, ranked recommendations with confidence scores
- **Time & Cost Savings**: Reduces trial-and-error by matching tools to specific use cases

## Target Users

- 📚 **Students**: Finding academic and productivity tools
- 🔬 **Researchers**: Discovering specialized research software
- 💼 **Knowledge Workers**: Selecting professional tools for specific tasks

## Technical Stack

### Backend Technologies
- **Runtime**: Node.js (v14+)
- **Framework**: Express.js 4.18.2
- **Database**: SQLite3 5.1.6
- **Authentication**: express-session 1.17.3, bcryptjs 2.4.3
- **Security**: helmet, cors, express-rate-limit
- **AI Integration**: Google Gemini 2.5 Flash API

### Frontend Technologies
- **Core**: HTML5, CSS3, JavaScript (ES6+)
- **CSS Framework**: Tailwind CSS
- **Icons**: Font Awesome 6.4.0
- **PWA**: Service Worker, Web App Manifest


## Frontend Runtime Asset Paths

The Express server serves static files from `public/`, so runtime asset URLs in HTML are resolved from that static root (for example `js/results.js` maps to `public/js/results.js`).

### Runtime entry pages (`public/*.html`)
- `public/index.html` → `js/main-updated.js`, `js/disclaimer.js`
- `public/results.html` → `js/results.js`, `js/disclaimer.js`, `./manifest.json`
- `public/dashboard.html` → `js/dashboard.js`, `js/disclaimer.js`
- `public/login.html` → `js/disclaimer.js` (page logic is inline)
- `public/register.html` → `js/disclaimer.js` (page logic is inline)

### Canonical frontend JavaScript files
- **Canonical runtime files** are under `public/js/` because that is the configured static root.
- `public/js/main-updated.js` is the active homepage script referenced by `public/index.html`.
- `public/js/results.js` is the active results page script referenced by `public/results.html`.
- Duplicate root-level `js/main.js` and `js/results.js` were removed to avoid ambiguity; runtime scripts are only under `public/js/`.

### Frontend file map
- `public/index.html` → homepage UI
- `public/results.html` → recommendation results UI
- `public/dashboard.html` → authenticated dashboard/settings UI
- `public/login.html` / `public/register.html` → auth flows
- `public/js/main-updated.js` → homepage behavior
- `public/js/results.js` → results rendering/actions
- `public/js/dashboard.js` → dashboard behavior
- `public/js/disclaimer.js` → shared disclaimer/banner behavior
- `public/manifest.json` and `public/sw.js` → PWA assets served at `/manifest.json` and `/sw.js`
- Service worker source of truth is **only** `public/sw.js` (no duplicate root `sw.js`) to prevent branch drift/conflicts

## Installation & Setup

### Prerequisites
- **Node.js**: Version 14.0.0 or higher ([Download](https://nodejs.org/))
- **npm**: Comes with Node.js

### Quick Start Guide

#### Windows Quick Start
```batch
# 1. Navigate to project directory
# 2. Run the batch file:
start.bat
```

The batch file automatically:

✅ Checks Node.js installation  
✅ Installs all dependencies  
✅ Creates database directory  
✅ Initializes SQLite database  
✅ Creates default .env file  
✅ Starts the server  

#### Manual Installation

1. **Install dependencies:**
```bash
npm install
```

2. **Initialize database:**
```bash
npm run init-db
```

3. **Create environment file:**
Create a `.env` file in the root directory:
```env
NODE_ENV=development
PORT=3001
SESSION_SECRET=your-random-string-change-this
FRONTEND_URL=http://localhost:3001
DB_PATH=./database/tool-finder.db
BCRYPT_ROUNDS=12
```

4. **Start the server:**
```bash
# Development mode
npm run dev

# Production mode
npm start
```

5. **Access the application:**
Open your browser and navigate to `http://localhost:3001` or the port that you set

## Disclaimer

**Disclaimer:** This is a prototype developed for the Eureka Juniors Stage 2 intermediate task. Created by Abhi Ram (ID: Ej25n566393, Email: a.m.s.s.abhiram492@gmail.com) as part of Team ID: EJ25T518480, Team Name: "Abhi's team".
