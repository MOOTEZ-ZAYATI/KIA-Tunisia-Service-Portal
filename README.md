# KIA Tunisia Service Portal

Started as a university project but I got way too into it and ended up building a full production system. What was supposed to be a basic CRUD app turned into a complete dealership management platform with 3D models, real time chat, OCR scanning, and way more features than my professor asked for.



## What This Thing Does

Built a web app for KIA dealerships that handles pretty much everything from the customer side to internal operations. Users can register their cars, book service appointments, track maintenance history. Agents get dashboards to manage all the customer requests, inventory, and business metrics.

The main features:

**Customer Portal**
Vehicle registration with VIN scanning using OCR (takes a photo of your VIN plate and it auto fills everything), book maintenance appointments, view your service history, get notifications via SMS and email!

**Agent Front Office**  
Manage customer accounts, handle appointment scheduling, real time chat system to talk with customers, view all service requests

**Agent Back Office**  
Full inventory management for car parts (8+ categories), pricing controls, analytics dashboard with revenue tracking, export reports as PDF, low stock alerts

**Authentication System**  
SMS and email OTP verification, JWT tokens for sessions, role based access (users vs agents vs admin)

## Tech Stack

I went with Next.js 14 because I wanted to learn the new App Router and honestly it was a pain at first but ended up being worth it. TypeScript everywhere because I got tired of runtime errors.

**Frontend**  
Next.js 14 with React 18 and TypeScript  
Tailwind CSS because writing CSS files is boring  
Three.js + React Three Fiber for the 3D car models (this was the fun part)  
GSAP for smooth animations  
Zustand for state because Redux is overkill  

**Backend**  
Next.js API routes (same repo, no separate backend server)  
Prisma as the ORM (way better than writing SQL by hand)  
PostgreSQL for the database  
JWT for auth with bcrypt for password hashing  
Socket.io for real time chat features  

**External APIs**  
OCR.space for scanning VIN numbers from images  
Twilio for sending SMS codes  
Resend for email notifications  

**Tools**  
ESLint and TypeScript for catching bugs  
Prisma Studio for checking the database visually  

## Architecture


Pretty straightforward setup. Users hit the Next.js frontend, which talks to API routes in the same app. API routes use Prisma to query PostgreSQL. Authentication is JWT based, stored in HTTP only cookies.

```
Customer Portal  →  Next.js App  →  API Routes  →  Prisma  →  PostgreSQL
Agent Dashboard  ↗                      ↓
                                  External APIs
                              (OCR, Twilio, Resend)
```

Database has tables for Users, Vehicles, Reservations, Service Records, Fleet Inventory, and OTP Codes. Set up foreign keys properly so deleting a user cascades correctly.

## How The Main Features Work

**VIN Scanning**  
User uploads a pic of their VIN plate. Frontend sends it to OCR.space API. Parse the VIN string, extract the year and model info. Auto populate the registration form. If OCR fails or gives garbage results, user can just type it manually.

**Inventory Management**  
Agents can add, edit, or delete parts from the inventory. Each item has SKU, name, category, quantity, price, supplier info. Set a minimum stock level and the system sends alerts when it gets low. Supports images for the parts catalog. Updates happen in real time so if two agents are looking at inventory, they see the same data.

**Real-time Chat**  
WebSocket connection using Socket.io. When a customer sends a message, it instantly shows up on the agent dashboard. Agent replies go straight back to the customer. Connection handles reconnects automatically if the network drops.

## Project Structure

Organized it like a typical Next.js 14 app with the App Router:

```
kia-tunisia-portal/
├── src/
│   ├── app/              # Pages and routing
│   │   ├── api/          # Backend endpoints
│   │   ├── user/         # Customer pages
│   │   ├── agent/        # Agent dashboard
│   │   └── auth/         # Login/signup pages
│   ├── components/       # React components
│   │   ├── ui/           # Buttons, cards, modals
│   │   ├── 3d/           # Three.js stuff
│   │   └── forms/        # Form components
│   ├── lib/              # Helper functions
│   │   ├── auth.ts       # JWT stuff
│   │   ├── prisma.ts     # Database connection
│   │   └── validators.ts # Input validation
│   ├── hooks/            # Custom hooks
│   └── types/            # TypeScript types
├── prisma/
│   └── schema.prisma     # Database schema
├── public/
│   ├── models/           # 3D car models (.glb files)
│   └── cars/             # Car images
└── .env                  # API keys 
```


## Security

Tried to cover the basics:

Passwords get hashed with bcrypt before storing  
JWTs expire after 7 days and stored in HTTP-only cookies so JavaScript can't access them  
All inputs validated with Zod schemas  
Prisma prevents SQL injection by default with parameterized queries  
React handles XSS protection automatically  
Set SameSite cookie attribute for CSRF protection  
Added rate limiting on auth endpoints so people can't spam OTP requests  
Role based permissions (regular users can't access agent endpoints)  



## Things I Learned Building This

TypeScript generics for API responses were confusing at first but now I get why they're useful  

Next.js 14 App Router is very different from Pages Router, had to relearn routing and data fetching  

Prisma schema design matters a lot. Had to refactor the database twice because I didn't think through relationships properly  

OCR is not 100% accurate, always need a fallback  

WebSocket connections can drop randomly, need reconnection logic  

Three.js is heavy, had to lazy load the 3D models or initial page load took forever  

JWT expiry needs to be handled gracefully on the frontend  


## Screenshots
<img width="1763" height="2425" alt="Capture d’écran_16-6-2026_152344_localhost" src="https://github.com/user-attachments/assets/e249d12a-aef8-4e13-a663-3e1287463f53" />
<img width="1763" height="865" alt="Capture d’écran_16-6-2026_15252_localhost" src="https://github.com/user-attachments/assets/047fd253-3b1f-4c03-aeb2-9a6da67bfec2" />
<img width="1763" height="990" alt="Capture d’écran_16-6-2026_152619_localhost" src="https://github.com/user-attachments/assets/cf396812-615a-4494-914d-f7518e05f4e6" />
<img width="1763" height="844" alt="Capture d’écran_16-6-2026_152640_localhost" src="https://github.com/user-attachments/assets/0121cdf1-853f-4df7-a030-25fc0991aa33" />
<img width="1763" height="1069" alt="Capture d’écran_16-6-2026_15275_localhost" src="https://github.com/user-attachments/assets/59989ef4-05ba-431a-837f-f83e9a099759" />
<img width="1763" height="2017" alt="Capture d’écran_16-6-2026_152754_localhost" src="https://github.com/user-attachments/assets/81ea7d21-0d06-4256-a4d8-5e38d68d8836" />
<img width="1763" height="1533" alt="Capture d’écran_16-6-2026_152832_localhost" src="https://github.com/user-attachments/assets/3cf205f8-c757-4ac3-b203-953549285e81" />
<img width="1763" height="1328" alt="Capture d’écran_16-6-2026_152852_localhost" src="https://github.com/user-attachments/assets/faaa82ad-2543-4ee5-8a48-5501c08e44f9" />
<img width="1763" height="844" alt="Capture d’écran_16-6-2026_15299_localhost" src="https://github.com/user-attachments/assets/90d805e9-101d-4be1-9ece-523b474ffbf1" />
<img width="1763" height="844" alt="Capture d’écran_16-6-2026_152928_localhost" src="https://github.com/user-attachments/assets/d1e4240d-e236-4e4b-9309-374bdb013596" />
<img width="1763" height="1516" alt="Capture d’écran_16-6-2026_153048_localhost" src="https://github.com/user-attachments/assets/b771d3d4-2042-4725-a6e2-cc085407ee02" />
<img width="1763" height="844" alt="Capture d’écran_16-6-2026_15317_localhost" src="https://github.com/user-attachments/assets/cb9f262e-5114-4f58-aeeb-188c4fb5c387" />
<img width="1763" height="858" alt="Capture d’écran_16-6-2026_153119_localhost" src="https://github.com/user-attachments/assets/f17cba65-cd07-4185-a152-aa38b375c223" />
<img width="1763" height="1291" alt="Capture d’écran_16-6-2026_153140_localhost" src="https://github.com/user-attachments/assets/76cce1a1-2ca3-4dd4-a5ae-15bc76f54b50" />
<img width="1763" height="1383" alt="Capture d’écran_16-6-2026_153154_localhost" src="https://github.com/user-attachments/assets/f03c4ab0-fa1f-4551-a06d-f624019a4b83" />
<img width="1763" height="967" alt="Capture d’écran_16-6-2026_15327_localhost" src="https://github.com/user-attachments/assets/a44657bc-c218-4d79-9446-ca4af0876121" />
<img width="1763" height="1458" alt="Capture d’écran_16-6-2026_153220_localhost" src="https://github.com/user-attachments/assets/29ccacd9-5425-41e8-9da4-b59a1e2e399a" />


## Contact

Built by Mohamed Mootez Zayati  
-> zayatimootez.it@gmail.com
If you're looking at this for hiring/evaluation purposes: yes I can build full stack applications, yes I know modern web dev, and yes I might have spent too much time on features that weren't required but that's just how I work.

