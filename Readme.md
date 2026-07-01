# FitZone — Fullstack Fitness Website

A fullstack fitness tracking and coaching platform built with **Next.js (App Router)** and **MongoDB**. Users can create workout plans, log exercises, track progress, follow nutrition guides, and (optionally) subscribe to premium content.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Database Schema](#database-schema)
- [API Routes](#api-routes)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Development Workflow](#development-workflow)
- [Authentication](#authentication)
- [Deployment](#deployment)
- [Roadmap](#roadmap)
- [Scripts](#scripts)
- [Contributing](#contributing)

---

## Features

- 🔐 User authentication (signup/login/logout, session management)
- 🏋️ Create and manage custom workout plans
- 📅 Daily/weekly workout scheduling and calendar view
- 📊 Progress tracking (weight, reps, sets, body measurements, charts)
- 🍎 Nutrition/meal logging
- 👤 User profile with avatar and stats
- 🔍 Exercise library with search and filters (muscle group, equipment)
- 📱 Fully responsive, mobile-first UI
- 🌙 Dark mode support

---

## Tech Stack

| Layer          | Technology                                   |
|----------------|-----------------------------------------------|
| Framework      | Next.js 14+ (App Router, Server Actions)      |
| Language       | TypeScript                                    |
| Database       | MongoDB (Atlas) + Mongoose ODM                |
| Styling        | Tailwind CSS                                  |
| Auth           | NextAuth.js (Auth.js) / JWT                   |
| State (client) | React Context / Zustand                       |
| Forms          | React Hook Form + Zod validation              |
| Charts         | Recharts                                      |
| File Storage   | Cloudinary / AWS S3 (for avatars, images)     |
| Deployment     | Vercel (app) + MongoDB Atlas (DB)             |

---

## Project Structure

```
fitzone/
├── app/
│   ├── (auth)/
│   │   ├── login/page.tsx
│   │   └── register/page.tsx
│   ├── (dashboard)/
│   │   ├── dashboard/page.tsx
│   │   ├── workouts/
│   │   │   ├── page.tsx
│   │   │   └── [id]/page.tsx
│   │   ├── nutrition/page.tsx
│   │   ├── progress/page.tsx
│   │   └── profile/page.tsx
│   ├── api/
│   │   ├── auth/[...nextauth]/route.ts
│   │   ├── workouts/route.ts
│   │   ├── workouts/[id]/route.ts
│   │   ├── exercises/route.ts
│   │   ├── nutrition/route.ts
│   │   └── users/route.ts
│   ├── layout.tsx
│   └── page.tsx
├── components/
│   ├── ui/                # Reusable UI components
│   ├── forms/
│   ├── charts/
│   └── layout/
├── lib/
│   ├── db.ts               # MongoDB connection helper
│   ├── auth.ts              # NextAuth config
│   ├── validators/          # Zod schemas
│   └── utils.ts
├── models/
│   ├── User.ts
│   ├── Workout.ts
│   ├── Exercise.ts
│   └── NutritionLog.ts
├── public/
├── styles/
├── .env.local
├── next.config.js
├── tailwind.config.ts
├── tsconfig.json
└── package.json
```

---

## Database Schema

### `User`
```ts
{
  name: string
  email: string (unique)
  password: string (hashed)
  avatar?: string
  height?: number
  weight?: number
  goals?: string[]
  createdAt: Date
}
```

### `Workout`
```ts
{
  userId: ObjectId (ref: User)
  title: string
  exercises: [
    {
      exerciseId: ObjectId (ref: Exercise)
      sets: number
      reps: number
      weight?: number
      restSeconds?: number
    }
  ]
  scheduledDate: Date
  completed: boolean
  createdAt: Date
}
```

### `Exercise`
```ts
{
  name: string
  muscleGroup: string
  equipment?: string
  difficulty: "beginner" | "intermediate" | "advanced"
  instructions: string
  videoUrl?: string
}
```

### `NutritionLog`
```ts
{
  userId: ObjectId (ref: User)
  date: Date
  meals: [
    {
      name: string
      calories: number
      protein: number
      carbs: number
      fats: number
    }
  ]
}
```

---

## API Routes

| Method | Route                      | Description                     |
|--------|-----------------------------|----------------------------------|
| POST   | `/api/auth/register`        | Register a new user              |
| POST   | `/api/auth/[...nextauth]`   | Login/session (NextAuth)         |
| GET    | `/api/workouts`             | Get all workouts for user        |
| POST   | `/api/workouts`             | Create a new workout             |
| GET    | `/api/workouts/[id]`        | Get single workout                |
| PUT    | `/api/workouts/[id]`        | Update workout                    |
| DELETE | `/api/workouts/[id]`        | Delete workout                    |
| GET    | `/api/exercises`            | List exercises (with filters)    |
| GET    | `/api/nutrition`            | Get nutrition logs                |
| POST   | `/api/nutrition`            | Add nutrition log entry          |
| GET    | `/api/users/me`             | Get current user profile         |
| PUT    | `/api/users/me`             | Update profile                    |

---

## Getting Started

### Prerequisites

- Node.js 18+
- npm / pnpm / yarn
- A MongoDB Atlas cluster (or local MongoDB instance)

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/SabbirCodes/Project-work-2.git
cd fitzone

# 2. Install dependencies
npm install

# 3. Set up environment variables
cp .env.example .env.local
# then fill in the values (see below)

# 4. Run the development server
npm run dev
```

Visit `http://localhost:3000` to view the app.

---

## Environment Variables

Create a `.env.local` file in the project root:

```env
# MongoDB
MONGODB_URI=mongodb+srv://<user>:<password>@cluster.mongodb.net/fitzone

# NextAuth
NEXTAUTH_SECRET=your_random_secret
NEXTAUTH_URL=http://localhost:3000

# Optional integrations
CLOUDINARY_URL=your_cloudinary_url
```

---

## Development Workflow

1. **Plan the schema** — finalize MongoDB models in `models/`.
2. **Build API routes** — implement CRUD logic under `app/api/`.
3. **Connect DB** — use a cached Mongoose connection in `lib/db.ts` to avoid multiple connections in dev (hot reload).
4. **Build UI** — start with the dashboard layout, then workouts, then nutrition/progress pages.
5. **Add auth** — protect dashboard routes using NextAuth middleware.
6. **Add validation** — use Zod schemas on both client (forms) and server (API routes).
7. **Test** — manually test each route with Postman/Thunder Client, then add integration tests.
8. **Polish UI/UX** — responsive design, loading states, error handling, dark mode.
9. **Deploy** — push to Vercel, connect MongoDB Atlas.

---

## Authentication

- Uses **NextAuth.js** with the Credentials provider (email/password) — can be extended with Google/GitHub OAuth.
- Passwords are hashed using **bcrypt** before saving to MongoDB.
- Protected routes use middleware (`middleware.ts`) to redirect unauthenticated users to `/login`.

---

## Deployment

### Vercel (recommended for Next.js)

1. Push your code to GitHub.
2. Import the repo into [Vercel](https://vercel.com).
3. Add all environment variables from `.env.local` into the Vercel project settings.
4. Deploy — Vercel automatically builds and hosts the app.

### MongoDB Atlas

1. Create a free cluster at [MongoDB Atlas](https://www.mongodb.com/atlas).
2. Whitelist Vercel's IP (or allow access from anywhere: `0.0.0.0/0` for simplicity).
3. Copy the connection string into `MONGODB_URI`.

---

## Roadmap

- [ ] Social features (follow friends, share workouts)
- [ ] AI-generated workout plans based on user goals
- [ ] Mobile app (React Native) sharing the same API
- [ ] Wearable device integration (Apple Health / Google Fit)
- [ ] Leaderboards and challenges
- [ ] Push notifications for workout reminders

---

## Scripts

```bash
npm run dev       # Start development server
npm run build     # Build for production
npm run start     # Start production server
npm run lint      # Run ESLint
npm run seed      # Seed database with sample exercises (optional script)
```

---

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/my-feature`)
3. Commit your changes (`git commit -m "Add my feature"`)
4. Push to the branch (`git push origin feature/my-feature`)
5. Open a Pull Request#   P r o j e c t - w o r k - 2  
 