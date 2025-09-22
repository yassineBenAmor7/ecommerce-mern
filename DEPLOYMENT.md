My Deployment Guide (MERN)

What I use

Backend: Render (Node 18+)
Database: MongoDB Atlas (free cluster)
Frontend: Netlify (Vite/React)
I prepare my database (MongoDB Atlas)
I create a free cluster and a DB user.
I allow IP addresses (for setup I start with 0.0.0.0/0, then I restrict).
I copy my connection string: mongodb+srv://USER:PASS@cluster0.xxxx.mongodb.net/ecommerce_mern
I will use it as MONGO_URI on the backend.
I deploy my backend (Render)
I connect my GitHub repo and create a Web Service pointing to my server entry (e.g. server.js).
Start command: node server.js
My app must listen on process.env.PORT.
I set my environment variables on Render:
NODE_ENV=production
MONGO_URI=
JWT_SECRET=
JWT_EXPIRE=30d
JWT_COOKIE_EXPIRE=30
(Optional email) SMTP_HOST, SMTP_PORT, SMTP_EMAIL, SMTP_PASSWORD, EMAIL_FROM
FRONTEND_URL=https://my-frontend.netlify.app
I deploy and note my backend URL, e.g. https://my-api.onrender.com
I secure CORS on my backend
I only allow my frontend origin: import cors from 'cors'; app.use(cors({ origin: process.env.FRONTEND_URL, credentials: true, }));
I deploy my frontend (Netlify)
Base directory: client
Build command: npm run build
Publish directory: dist
I add my frontend env var:
VITE_API_BASE_URL=https://my-api.onrender.com
I deploy and note my frontend URL, e.g. https://my-frontend.netlify.app
I ensure FRONTEND_URL on Render matches exactly this URL (protocol + domain), then I redeploy the backend.
I wire my client to the API
I centralize Axios with my base URL: import axios from 'axios'; export const api = axios.create({ baseURL: import.meta.env.VITE_API_BASE_URL, });
I replace any hardcoded /api calls in production with api.(path) or prepend VITE_API_BASE_URL.
I test my production setup
Products: GET /api/v1/products → 200
Auth: POST /api/v1/auth/login → 200, then GET /api/v1/auth/me → 200
Profile update: PUT /api/v1/auth/updatedetails → 200
Wishlist: liked items appear on /wishlist and in /profile > Wishlist (same list and count)
Protected routes redirect to /login when logged out
Payment page opens from /profile > Payment Methods > Add Payment Method
Troubleshooting I use

CORS errors: I ensure FRONTEND_URL matches my Netlify domain exactly (https + domain).
Requests to localhost in prod: I fix VITE_API_BASE_URL in Netlify, then rebuild/redeploy.
Mongo connection errors: I verify MONGO_URI and Atlas Network Access (IP allowlist).
/auth/me 500: I check Render logs, improve token middleware, return proper 4xx instead of 500 for invalid token.
Notes

I never commit secrets. I store production env vars in Render/Netlify dashboards.
After changing env vars on the frontend, I always rebuild and redeploy.
