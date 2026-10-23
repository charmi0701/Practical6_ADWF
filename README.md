# Practical 6: Full Stack Integration (React + Node + MongoDB)

A task manager that connects a React interface to an Express/Mongoose API. Tasks are saved in MongoDB and remain available after a page refresh. The interface includes create, read, update, delete, completion toggles, optimistic creation with rollback on failure, delete confirmation, per-operation feedback, and success/error notifications.

## Project structure

```text
practical-6/
├── backend/   Express API, Mongoose model, CORS
└── frontend/  React + Vite task interface and shared API client
```

## Requirements

- Node.js 18 or newer and npm
- MongoDB Community Server running locally, or a MongoDB Atlas URI

## Configure and run

1. In `backend`, copy `.env.example` to `.env`. Set `MONGO_URI` to your local or Atlas database connection string. Keep `.env` private.
2. Start the backend in a terminal:

   ```bash
   cd practical-6/backend
   npm install
   npm run dev
   ```

   The API listens on `http://localhost:5000`. It waits for MongoDB before accepting requests. CORS allows Vite on `http://localhost:5173` or `http://localhost:5174` by default (5174 is used automatically when 5173 is busy). Set `CLIENT_ORIGIN` in `.env` to a comma-separated list if your frontend uses other origins.

3. In another terminal, start the frontend:

   ```bash
   cd practical-6/frontend
   npm install
   npm run dev
   ```

   Open the URL Vite prints (normally `http://localhost:5173`). The API URL defaults to `http://localhost:5000`; to change it, copy `.env.example` to `.env` and set `VITE_API_URL`.

## API routes

| Method | Path | Action |
| --- | --- | --- |
| GET | `/tasks` | List tasks |
| GET | `/tasks/:id` | Read one task |
| POST | `/tasks` | Create task |
| PUT | `/tasks/:id` | Update fields or completion |
| DELETE | `/tasks/:id` | Delete task |

Task shape: `{ "title": "...", "description": "...", "priority": "low|medium|high", "completed": false }`. Mongoose validation errors return HTTP 400; missing tasks return 404. The UI updates from API responses and reports failed operations. New tasks appear optimistically and are removed if the server rejects the save.

## Practical analysis / viva notes

- CORS middleware must run before API routes so browsers accept requests from the Vite origin. The default configuration is limited to the local frontend origin.
- After POST, PUT, and DELETE, the interface uses the returned task or successful delete response to synchronize visible state. Silent assumptions can show changes the database did not save.
- Write failures need visible handling because otherwise users may believe data was persisted when the API rejected the request or was unavailable. Optimistic creation is rolled back if the POST fails.
- Refreshing the browser triggers `GET /tasks`; persistence is provided by MongoDB through Mongoose, not by React component state.

## Troubleshooting

- **Network error:** start the backend and confirm it is listening on port 5000.
- **MongoDB connection error:** check that MongoDB is running and that `MONGO_URI` is valid.
- **CORS error:** check `CLIENT_ORIGIN` and that it matches the frontend origin exactly.
- **Stale data:** use Sync to fetch `/tasks`, then inspect the backend response and MongoDB connection.

## Result

The React frontend and Node/Express/MongoDB backend are integrated through one shared API client, with database persistence and CRUD feedback in the interface.
