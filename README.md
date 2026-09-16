# InterviewAI — AI-Powered Career Preparation Platform

<div align="center">

![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)
![Express](https://img.shields.io/badge/Express-000000?style=for-the-badge&logo=express&logoColor=white)
![React](https://img.shields.io/badge/React-61DAFB?style=for-the-badge&logo=react&logoColor=black)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=for-the-badge&logo=mongodb&logoColor=white)
![Gemini AI](https://img.shields.io/badge/Gemini_AI-8E75B2?style=for-the-badge&logo=google&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-646CFF?style=for-the-badge&logo=vite&logoColor=white)
![JWT](https://img.shields.io/badge/JWT-000000?style=for-the-badge&logo=jsonwebtokens&logoColor=white)
![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)

A full-stack Gen AI career preparation platform that analyzes your resume against job descriptions and generates personalized interview strategies, tailored questions, skill gap analysis, and ATS-optimized resumes.

</div>

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [API Endpoints](#api-endpoints)
- [Database Schema](#database-schema)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Environment Variables](#environment-variables)
  - [Running the Project](#running-the-project)
- [Project Structure](#project-structure)
- [License](#license)

---

## Features

- **🔐 Authentication** — Secure register/login/logout with JWT cookie-based auth and token blacklisting
- **📊 AI Interview Report** — Upload your resume PDF + paste a job description → get a comprehensive interview preparation report including:
  - **Match Score** (0–100%) — How well your profile fits the role
  - **Technical Questions** — Predicted interview questions with interviewer intentions and model answers
  - **Behavioral Questions** — Soft-skill questions with intentions and suggested answers
  - **Skill Gap Analysis** — Identified gaps with severity ratings (low / medium / high)
  - **Preparation Roadmap** — Day-by-day study plan with actionable tasks
- **📄 ATS Resume Generator** — Generate a tailored, ATS-optimized resume as a downloadable PDF
- **📋 Report Dashboard** — View and revisit all past interview reports sorted by date
- **🔒 Protected Routes** — Unauthenticated users are automatically redirected to login

---

## Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | React 19, Vite 7, SCSS, React Router 7, Axios |
| **Backend** | Node.js, Express 5 |
| **Database** | MongoDB (Mongoose 9) |
| **AI** | Google Gemini API (`@google/genai`) |
| **Auth** | JWT (`jsonwebtoken`), bcryptjs |
| **Validation** | Zod, zod-to-json-schema |
| **File Handling** | Multer (memory storage), pdf-parse |
| **PDF Generation** | Puppeteer (headless Chrome) |

---

## Architecture

```
┌─────────────────────────┐         ┌────────────────────────────────────────┐
│    FRONTEND (React)     │  HTTP   │           BACKEND (Express)            │
│    Vite Dev Server      │────────▶│                                        │
│    Port 5173            │  REST   │  Routes → Middleware → Controller      │
│                         │◀────────│              ↓                         │
│  • Auth (Context/Hooks) │ JSON +  │  Services (AI Service) ←→ Gemini API   │
│  • Interview (Context)  │ PDF     │              ↓                         │
│  • Protected Routes     │         │  Models (Mongoose)    ←→ MongoDB       │
└─────────────────────────┘         └────────────────────────────────────────┘
```

The backend follows **MVC-S (Model-View-Controller-Service)** pattern. The frontend uses a **feature-based architecture** with collocated context, hooks, API services, and pages per feature.

---

## API Endpoints

### Auth — `/api/auth`

| Method | Endpoint | Access | Description |
|---|---|---|---|
| `POST` | `/register` | Public | Register a new user |
| `POST` | `/login` | Public | Login with email & password |
| `GET` | `/logout` | Public | Logout & blacklist token |
| `GET` | `/get-me` | Private | Get current user details |

### Interview — `/api/interview`

| Method | Endpoint | Access | Description |
|---|---|---|---|
| `POST` | `/` | Private | Generate AI interview report (multipart: resume + job desc + self desc) |
| `GET` | `/` | Private | List all reports for logged-in user |
| `GET` | `/report/:interviewId` | Private | Get a single report by ID |
| `POST` | `/resume/pdf/:interviewReportId` | Private | Generate & download tailored resume PDF |

---

## Database Schema

### Users
```
username  — String, required, unique
email     — String, required, unique
password  — String, required (bcrypt hashed)
```

### Interview Reports
```
user              — ObjectId (ref: users)
title             — String (AI-generated job title)
jobDescription    — String
resume            — String (extracted text)
selfDescription   — String
matchScore        — Number (0–100)
technicalQuestions — [{ question, intention, answer }]
behavioralQuestions — [{ question, intention, answer }]
skillGaps         — [{ skill, severity: "low"|"medium"|"high" }]
preparationPlan   — [{ day, focus, tasks: [String] }]
createdAt / updatedAt — Timestamps
```

### Blacklisted Tokens
```
token     — String (invalidated JWT)
createdAt — Timestamp
```

---

## Getting Started

### Prerequisites

Make sure you have the following installed:

- **Node.js** (v18 or higher) — [Download](https://nodejs.org/)
- **MongoDB** — Local instance or [MongoDB Atlas](https://www.mongodb.com/atlas) (cloud)
- **Google Gemini API Key** — [Get one here](https://aistudio.google.com/apikey)
- **Git** — [Download](https://git-scm.com/)

### Installation

1. **Clone the repository**

   ```bash
   git clone https://github.com/mee0210/interview_ai.git
   cd interview_ai
   ```

2. **Install backend dependencies**

   ```bash
   cd Backend
   npm install
   ```

3. **Install frontend dependencies**

   ```bash
   cd ../Frontend
   npm install
   ```

### Environment Variables

Create a `.env` file inside the `Backend/` directory:

```bash
# Backend/.env

MONGO_URI=mongodb://localhost:27017/interviewai
JWT_SECRET=your_jwt_secret_key_here
GOOGLE_GENAI_API_KEY=your_gemini_api_key_here
```

| Variable | Description |
|---|---|
| `MONGO_URI` | MongoDB connection string (local or Atlas) |
| `JWT_SECRET` | Secret key for signing JWT tokens (use a strong random string) |
| `GOOGLE_GENAI_API_KEY` | Your Google Gemini API key from [AI Studio](https://aistudio.google.com/apikey) |

### Running the Project

You need **two terminals** — one for the backend and one for the frontend.

**Terminal 1 — Start the Backend:**

```bash
cd Backend
npm run dev
```

The server will start on **http://localhost:3000**.

**Terminal 2 — Start the Frontend:**

```bash
cd Frontend
npm run dev
```

The app will open on **http://localhost:5173**.

> **Note:** Make sure MongoDB is running before starting the backend. If using MongoDB Atlas, your `MONGO_URI` connection string should already point to the cloud cluster.

### Usage

1. Open **http://localhost:5173** in your browser
2. **Register** a new account or **Login** with existing credentials
3. Paste a **Job Description** in the left panel
4. Upload your **Resume (PDF)** or write a quick **Self-Description** in the right panel
5. Click **"Generate My Interview Strategy"**
6. View your personalized report with technical questions, behavioral questions, skill gaps, and a preparation roadmap
7. Click **"Download Resume"** to get a tailored, ATS-optimized resume PDF

---

## Project Structure

```
interviewai/
├── Backend/
│   ├── server.js                          # Entry point
│   ├── package.json
│   └── src/
│       ├── app.js                         # Express app setup (CORS, routes)
│       ├── config/
│       │   └── database.js                # MongoDB connection
│       ├── controllers/
│       │   ├── auth.controller.js         # Register, Login, Logout, GetMe
│       │   └── interview.controller.js    # Report generation, retrieval, PDF
│       ├── middlewares/
│       │   ├── auth.middleware.js          # JWT verification + blacklist check
│       │   └── file.middleware.js          # Multer config (3MB, memory storage)
│       ├── models/
│       │   ├── user.model.js              # User schema
│       │   ├── interviewReport.model.js   # Interview report with subdocuments
│       │   └── blacklist.model.js         # Token blacklist schema
│       ├── routes/
│       │   ├── auth.routes.js             # /api/auth/* routes
│       │   └── interview.routes.js        # /api/interview/* routes
│       └── services/
│           └── ai.service.js              # Gemini AI integration + Puppeteer PDF
│
├── Frontend/
│   ├── index.html
│   ├── vite.config.js
│   ├── package.json
│   └── src/
│       ├── main.jsx                       # React entry point
│       ├── App.jsx                        # Root component with providers
│       ├── app.routes.jsx                 # React Router config
│       ├── style.scss                     # Global styles
│       └── features/
│           ├── auth/
│           │   ├── auth.context.jsx       # Auth context provider
│           │   ├── auth.form.scss         # Auth form styles
│           │   ├── components/
│           │   │   └── Protected.jsx      # Route guard component
│           │   ├── hooks/
│           │   │   └── useAuth.js         # Auth hook (login, register, logout)
│           │   ├── pages/
│           │   │   ├── Login.jsx
│           │   │   └── Register.jsx
│           │   └── services/
│           │       └── auth.api.js        # Auth API calls (Axios)
│           └── interview/
│               ├── interview.context.jsx  # Interview context provider
│               ├── hooks/
│               │   └── useInterview.js    # Interview hook (CRUD + PDF)
│               ├── pages/
│               │   ├── Home.jsx           # Report generation + dashboard
│               │   └── Interview.jsx      # Report viewer (questions, roadmap)
│               ├── services/
│               │   └── interview.api.js   # Interview API calls (Axios)
│               └── style/
│                   ├── home.scss
│                   └── interview.scss
│
├── .gitignore
├── LICENSE
└── README.md
```

---

## License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

<div align="center">
  <p>Built with ❤️ by <a href="https://github.com/mee0210">mee0210</a></p>
</div>
