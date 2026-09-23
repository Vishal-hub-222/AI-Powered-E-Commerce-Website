# AuraMart AI

> A full-stack e-commerce experience with intelligent product discovery, a conversational shopping assistant, curated product bundles, and an operations dashboard.

![React](https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-6-646CFF?logo=vite&logoColor=white)
![Express](https://img.shields.io/badge/Express-4-000000?logo=express&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-8-47A248?logo=mongodb&logoColor=white)
![License](https://img.shields.io/badge/license-not%20specified-lightgrey)

AuraMart AI combines a polished React storefront with an Express and MongoDB API. Shoppers can search by intent (for example, “wireless headphones under $100”), get recommendations from Aura AI, manage a cart, check out, and review past orders. Administrators can manage products, orders, and users from a dedicated dashboard.

## Highlights

| Storefront | Intelligence | Operations |
| --- | --- | --- |
| Responsive catalog, filters, sorting, quick views, cart, checkout, and order history. | Intent-aware search, shopping-assistant recommendations, product-copy generation, review summaries, and complementary bundles. | JWT authentication, role-aware admin controls, product image uploads, inventory-aware orders, and customer/order management. |

## Tech stack

- **Frontend:** React 18, Vite, Tailwind CSS, Lucide icons
- **Backend:** Node.js, Express, Mongoose, JWT, Multer, Morgan
- **Services:** MongoDB and Cloudinary for optional product-image uploads

## Architecture

```text
browser
  │
  ├── React + Vite client (port 5173)
  │       └── /api proxy in development
  │
  └── Express API (port 4000)
          ├── MongoDB: users, products, reviews, orders
          └── Cloudinary: uploaded product images
```

## Quick start

### Prerequisites

- Node.js **18+**
- A MongoDB database (local or Atlas)
- A Cloudinary account only when uploading product images through the admin interface

### 1. Clone and install

```bash
git clone <your-repository-url>
cd AI-Powered-E-Commerce-Website

npm install --prefix server
npm install --prefix client
```

### 2. Configure environment variables

Create `server/.env` (or a repository-root `.env`) with the following values:

```dotenv
# Required
MONGO_URL=mongodb+srv://<username>:<password>@<cluster>/<database>
JWT_SECRET=replace-with-a-long-random-secret

# Optional — required only for Cloudinary-backed file uploads
CLOUD_NAME=your-cloud-name
CLOUD_API_KEY=your-api-key
CLOUD_API_SECRET=your-api-secret

# Optional
PORT=4000
NODE_ENV=development
```

> **Security note:** Never commit `.env` files. Set a strong `JWT_SECRET` for every non-local environment.

### 3. Seed demo content (optional)

```bash
npm run seed --prefix server
```

This clears the application collections before adding sample products, reviews, and demo accounts. Use it only with a disposable development database.

### 4. Start the application

Use two terminals:

```bash
# Terminal 1 — API
npm run dev --prefix server
```

```bash
# Terminal 2 — web client
npm run dev --prefix client
```

Open [http://localhost:5173](http://localhost:5173). The Vite development server forwards `/api` requests to `http://localhost:4000`.

## Scripts

| Location | Command | Purpose |
| --- | --- | --- |
| `client` | `npm run dev` | Start the Vite development server. |
| `client` | `npm run build` | Create a production client build in `client/dist`. |
| `client` | `npm run preview` | Preview the production client build. |
| `server` | `npm start` | Start the Express API. |
| `server` | `npm run dev` | Start the API with Nodemon. |
| `server` | `npm run seed` | Reset and populate the database with sample data. |

## API overview

All endpoints are rooted at `/api`. Protected endpoints expect `Authorization: Bearer <token>`.

| Area | Endpoints | Access |
| --- | --- | --- |
| Health | `GET /health` | Public |
| Authentication | `POST /auth/register`, `POST /auth/login`, `GET /auth/profile`, `PUT /auth/preferences` | Public / authenticated |
| Products | `GET /products`, `GET /products/categories/all`, `GET /products/:id`, `POST /products/:id/reviews` | Public / authenticated for reviews |
| Product administration | `POST /products`, `PUT /products/:id`, `DELETE /products/:id` | Admin |
| Orders | `POST /orders`, `GET /orders/my-orders`, `GET /orders/:id` | Authenticated for create/history; product order lookup is public |
| Order administration | `GET /orders/all`, `PUT /orders/:id/status` | Admin |
| User administration | `GET /auth/users` | Admin |
| AI tools | `POST /ai/assistant`, `POST /ai/smart-search`, `POST /ai/generate-description`, `GET /ai/bundle/:productId`, `GET /ai/review-summary/:productId` | Public |

### Example: intent-aware search

```bash
curl -X POST http://localhost:4000/api/ai/smart-search \
  -H 'Content-Type: application/json' \
  -d '{"query":"noise cancelling headphones under 150"}'
```

The AI endpoints use application-side intent parsing and catalog/review data to tailor results; they do not require an external model API key.

## Production deployment

1. Build the client: `npm run build --prefix client`.
2. Set `NODE_ENV=production` and the environment variables listed above.
3. Start the server from `server`; in production it serves `client/dist` as the frontend and exposes the API on `PORT` (default `4000`).
4. Use a managed MongoDB deployment and configure Cloudinary credentials if administrators will upload image files.

## Project structure

```text
.
├── client/                 # React storefront and admin dashboard
│   └── src/
│       ├── components/     # Customer-facing UI
│       ├── admin/          # Admin dashboard views
│       ├── context/        # Authentication and cart state
│       └── services/       # API client
└── server/                 # Express application
    ├── controllers/        # Business logic and AI helpers
    ├── middleware/         # JWT authorization and uploads
    ├── models/             # Mongoose schemas
    ├── routes/             # HTTP endpoints
    └── seed/               # Development sample data
```

## Development notes

- The client calls relative `/api` URLs. Vite proxies them locally; the Express server serves the built client when `NODE_ENV=production`.
- Product create and update routes accept up to five uploaded image files. Cloudinary is only used for file uploads; products can also be created with image URLs.
- The seed command prints demonstration credentials in the terminal. Treat those accounts as development-only and replace them before deployment.

## License

No license has been specified for this repository. Add one before redistributing or using this project in production.
