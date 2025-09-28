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
PORT=3000
SESSION_SECRET=your-random-string-change-this
FRONTEND_URL=http://localhost:3000
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
Open your browser and navigate to `http://localhost:3000`

## Disclaimer

**Disclaimer:** This is a prototype developed for the Eureka Juniors Stage 2 intermediate task. Created by Abhi Ram (ID: Ej25n566393, Email: a.m.s.s.abhiram492@gmail.com) as part of Team ID: EJ25T518480, Team Name: "Abhi's team".