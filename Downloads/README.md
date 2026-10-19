# Project 2: Backend API Development

A simple, working REST API built with Node.js + Express, covering exactly
the brief from your slide deck:

- **Goal:** Develop a simple backend API to handle application logic.
- **Key requirements met:**
  - ✅ API endpoints (GET / POST, plus PUT / DELETE for completeness)
  - ✅ Handles user input and responses
  - ✅ Validates basic data (syntactic + semantic, per "The Gatekeeper Rule")
- **Key skills demonstrated:** backend development, server-side logic, API concepts.

## Project structure

```
backend-api-project/
├── server.js              # entry point — wires everything together
├── routes/
│   └── users.js           # RESTful endpoints for the "users" resource
├── middleware/
│   ├── validateUser.js     # syntactic + semantic input validation
│   └── errorHandler.js     # centralized 404 / error responses
├── data/
│   └── store.js            # in-memory "database" (swap for real DB later)
└── package.json
```

## Running it

```bash
npm install
npm start
# Server listening on http://localhost:3000
```

## Endpoints

| Method | Path         | Description             | Success | Failure          |
|--------|--------------|--------------------------|---------|-------------------|
| GET    | /health      | Health check             | 200     | —                 |
| GET    | /users       | List all users           | 200     | —                 |
| GET    | /users/:id   | Get one user              | 200     | 404 if not found  |
| POST   | /users       | Create a user             | 201     | 400 if invalid    |
| PUT    | /users/:id   | Update a user              | 200     | 400 / 404         |
| DELETE | /users/:id   | Delete a user              | 204     | 404 if not found  |

### Example requests

```bash
# List users
curl http://localhost:3000/users

# Create a user
curl -X POST http://localhost:3000/users \
  -H "Content-Type: application/json" \
  -d '{"name":"Grace Hopper","email":"grace@example.com","role":"admin"}'

# Invalid input -> 400 with details
curl -X POST http://localhost:3000/users \
  -H "Content-Type: application/json" \
  -d '{"email":"not-an-email"}'

# Delete a user -> 204 No Content
curl -X DELETE http://localhost:3000/users/2
```

## Design notes (mapping back to your slides)

- **RESTful naming:** resources are nouns (`/users`), HTTP methods are the
  verbs (`GET`, `POST`, `PUT`, `DELETE`) — no `/getUsers` or `/createUser`.
- **The Gatekeeper Rule ("never trust the client"):** `validateUser.js`
  checks structure first (syntactic — is `name` present, is `email` a
  string?), then content (semantic — is the email actually shaped like an
  email, is `role` one of the allowed values?).
- **Status codes as vocabulary:** 200 (OK), 201 (Created), 204 (No Content),
  400 (Bad Request), 404 (Not Found), 500 (Internal Server Error) are all
  used deliberately rather than defaulting everything to 200/500.
- **Client/server decoupling:** the store (`data/store.js`) is isolated
  behind a small function API, so swapping the in-memory array for a real
  database later doesn't require touching the routes.

## Next steps if you want to extend it

- Swap `data/store.js` for a real database (PostgreSQL, MongoDB, etc.)
- Add authentication middleware (401 Unauthorized / 403 Forbidden)
- Add rate limiting (429 Too Many Requests)
- Write automated tests (e.g., with `supertest` + `jest`)
- Add an API gateway in front of this service once you split it into
  multiple microservices, per the "Scaling the Mind" slide.
