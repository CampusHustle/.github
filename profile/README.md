# CampusHustle 🎓💼

**A peer-to-peer academic marketplace where university students earn from their knowledge.**

CampusHustle connects students who need help — tutoring, study materials, exam prep — with fellow students who have the skills to provide it. Instead of hiring expensive professional tutors, students learn from peers who recently mastered the same courses, understand the exact curriculum, and are motivated to build a reputation and earn income while doing it.

This isn't just a tutoring app. It's an **academic creator economy**: students monetizing their knowledge through live mentorship, study notes, and AI-assisted learning — all built and secured with a university-verified trust layer.

---

## Table of Contents

- [Problem & Vision](#problem--vision)
- [Core Features (MVP)](#core-features-mvp)
- [Future Roadmap](#future-roadmap)
- [Tech Stack](#tech-stack)
- [System Architecture](#system-architecture)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Project Structure](#project-structure)
- [Security Considerations](#security-considerations)
- [Contributing](#contributing)
- [License](#license)

---

## Problem & Vision

University students possess valuable, up-to-date academic knowledge, but have no structured way to monetize it while balancing their own studies. Meanwhile, other students need affordable, relatable help — from someone who took the exact same course, not a generic tutoring service.

CampusHustle solves both problems at once: a **verified, university-scoped marketplace** where students can tutor, sell notes, and get paid for what they already know — while learners get fast, affordable, peer-relevant help.

---

## Core Features (MVP)

### 🔐 Authentication & Profiles
- University email–verified signup and login
- JWT-based authentication with hashed passwords
- Student and Tutor profiles with skills, subjects, department, and year

### 🔍 Tutor Discovery
- Structured skill/subject tagging (searchable, not free text)
- Filter by subject, price, rating, and department
- Public tutor profiles showing rating, sessions completed, and verification status

### 📅 Booking
- Tutors set fixed weekly availability slots
- Students request a slot; tutors accept or decline
- Booking status tracking: pending → confirmed → completed / cancelled

### 💬 Private Chat
- Real-time 1:1 messaging (Socket.io), unlocked after a connection or booking is accepted
- Free-tier chat for coordination and quick questions
- Optional, consent-based, logged sharing of contact details within a chat thread

### 📚 Notes Marketplace
- Tutors upload study materials as PDFs — or photograph physical notes directly
- Photographed notes are converted to text via OCR (Tesseract.js) and packaged as downloadable PDFs
- Preview pages, course tagging, pricing, and purchase via integrated payment gateway

### 🤖 AI Study Assistant (RAG)
- Uploaded notes are chunked and embedded on upload
- Students can ask questions answered **only** from that tutor's uploaded material — no hallucinated answers from outside sources
- Built on a lightweight retrieval pipeline: no dedicated vector database required

### ⭐ Ratings & Trust
- Multi-dimensional reviews: knowledge, communication, punctuality
- Report and block functionality to protect users, especially around in-chat contact sharing

### 🔔 Notifications
- In-app alerts for new booking requests, accepted bookings, new messages, and note purchases

---

## Future Roadmap

The following are intentionally **out of MVP scope** but represent the platform's long-term direction:

- Subscription plans for recurring access to a tutor's content
- Group sessions and fixed-price crash courses
- Gamified tutor levels (Bronze → Diamond) and badges
- Live whiteboard, screen sharing, and collaborative code editor
- Recorded session playback
- Native mobile app (Flutter)
- Advanced analytics dashboards for tutors
- Automated ID verification pipeline (OCR + face-match confidence scoring with human-in-the-loop review)

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React (Vite) + Tailwind CSS |
| Backend | Node.js + Express |
| Real-time | Socket.io |
| Database | MongoDB (Atlas free tier) + Mongoose |
| Auth | JWT + bcrypt |
| File Storage | Cloudinary (free tier) |
| OCR | Tesseract.js |
| AI / Embeddings | Gemini API (embeddings + generation) |
| Payments | Chapa |
| Email | Nodemailer (Gmail SMTP) |
| Hosting | Vercel/Netlify (frontend), Render (backend) |

All third-party services are used strictly within their free tiers, in line with this project's scope as a learning and graduation project.

---

## System Architecture

```
┌─────────────┐        ┌──────────────┐        ┌───────────────┐
│   React App  │ <────> │  Express API │ <────> │   MongoDB      │
│ (Vite +      │  REST/ │  + Socket.io │        │   Atlas        │
│  Tailwind)   │  WS    │              │        │                │
└─────────────┘        └──────┬───────┘        └───────────────┘
                               │
                 ┌─────────────┼─────────────┐
                 │             │             │
          ┌──────▼─────┐ ┌────▼────┐  ┌─────▼──────┐
          │ Cloudinary │ │  Gemini  │  │   Chapa     │
          │ (files)    │ │ (AI/RAG) │  │ (payments)  │
          └────────────┘ └─────────┘  └────────────┘
```

**Notes upload → RAG pipeline flow:**

```
Upload (PDF or photo) 
   → [if photo] OCR text extraction (Tesseract.js)
   → Text chunking
   → Embedding generation (Gemini)
   → Stored in MongoDB with vector + metadata
   → Student question → embed query → cosine similarity search
   → Top matching chunks → Gemini generates grounded answer
```

---

## Getting Started

### Prerequisites
- Node.js (v18+)
- npm or yarn
- A MongoDB Atlas account (free tier)
- API keys: Gemini, Cloudinary, Chapa (as applicable)

### Installation

```bash
# Clone the repository
git clone https://github.com/dan-seng/campus-hustle.git
cd campus-hustle

# Install server dependencies
cd server
npm install

# Install client dependencies
cd ../client
npm install
```

### Running Locally

```bash
# Start the backend (from /server)
npm run dev

# Start the frontend (from /client)
npm run dev
```

The frontend will run on `http://localhost:5173` and the backend on `http://localhost:5000` by default.

---

## Environment Variables

Create a `.env` file in `/server` with the following:

```
PORT=5000
MONGODB_URI=your_mongodb_atlas_connection_string
JWT_SECRET=your_jwt_secret
GEMINI_API_KEY=your_gemini_api_key
CLOUDINARY_CLOUD_NAME=your_cloudinary_name
CLOUDINARY_API_KEY=your_cloudinary_key
CLOUDINARY_API_SECRET=your_cloudinary_secret
CHAPA_SECRET_KEY=your_chapa_key
EMAIL_USER=your_gmail_address
EMAIL_PASS=your_gmail_app_password
```

> Never commit `.env` files. A `.env.example` is provided as a template.

---

## Project Structure

```
campus-hustle/
├── client/                # React frontend
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── hooks/
│   │   └── services/
│   └── package.json
├── server/                 # Express backend
│   ├── controllers/
│   ├── models/
│   ├── routes/
│   ├── middleware/
│   ├── services/           # RAG pipeline, OCR, payments
│   └── package.json
├── docs/                   # SRS, architecture notes, diagrams
└── README.md
```

---

## Security Considerations

Given this project's emphasis on secure design:

- Passwords are hashed with bcrypt; raw passwords are never stored or logged
- JWTs are short-lived, with refresh token rotation
- Contact information shared in chat is consent-based and logged for audit purposes
- Rate limiting is applied to booking requests and message sends to prevent spam and abuse
- Uploaded files are validated and scanned before processing
- Role-based access control restricts actions by Student, Tutor, and Admin roles
- Sensitive future features (e.g., ID verification) are designed with data minimization in mind — processed rather than permanently stored where possible

---

## Contributing

This is currently a solo graduation project built as part of the INSA CTC Summer Camp. Contribution guidelines will be added if the project opens up to collaborators in the future.

---

## License

This project is currently unlicensed pending a decision on its future direction. All rights reserved by the author until a license is added.

---

**Built by Team እግረመንገድ** 
