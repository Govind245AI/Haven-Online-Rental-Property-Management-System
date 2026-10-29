# Haven — Rental Property Management

A local-first rental discovery and management app built with responsive HTML, Bootstrap, browser JavaScript, Express, MongoDB, and Mongoose. Azure deployment is intentionally out of scope.

## Requirements

- Node.js 20+
- MongoDB Community Server running locally, or a MongoDB connection string

## Run locally

1. Copy `.env.example` to `.env` and set `JWT_SECRET` to a random secret of at least 32 characters. For example, generate one with `node -e "console.log(require('node:crypto').randomBytes(48).toString('hex'))"`.
2. Set `MONGODB_URI` if MongoDB is not using the local default URI.
3. Install packages: `npm install`
4. Insert sample listings: `npm run seed`
5. Start the server: `npm run dev`
6. Open [http://localhost:3000](http://localhost:3000).

The server serves both the browser app and the API. Check `/api/health` to see API/database status. Sample listings are browseable; register an account to create and manage your own listings. They intentionally have no shared/default login credentials.

## Booking and demo checkout

Signed-in users can reserve a property for a date range, view their bookings, and cancel a reservation. Owners can see bookings received for their properties. The date range is checked against existing confirmed bookings. Checkout is **demo only**: it records a simulated reservation deposit equal to 25% of one month's rent, but it does not collect money, connect to a payment provider, or issue refunds. Email notifications are disabled.

## API

| Method | Path | Access | Purpose |
|---|---|---|---|
| GET | `/api/health` | Public | Server and database status |
| POST | `/api/auth/register` | Public | Register and receive a JWT |
| POST | `/api/auth/login` | Public | Sign in and receive a JWT |
| GET | `/api/auth/me` | Bearer JWT | Current account |
| GET | `/api/listings` | Public | Browse, filter, search, paginate |
| GET | `/api/listings/:id` | Public | Listing details |
| GET | `/api/listings/mine` | Bearer JWT | Current owner's listings |
| POST | `/api/listings` | Bearer JWT | Create an owned listing |
| PATCH | `/api/listings/:id` | Bearer JWT, owner | Update an owned listing |
| DELETE | `/api/listings/:id` | Bearer JWT, owner | Delete listing and its inquiries |
| POST | `/api/inquiries/listings/:listingId` | Public | Send an inquiry to the listing owner |
| GET | `/api/inquiries/mine` | Bearer JWT | View inquiries received by the owner |

Listing filters: `location`, `minPrice`, `maxPrice`, `type`, `q`, `page`, and `limit`. Combine filters as needed, for example `/api/listings?location=Manipal&maxPrice=25000&type=Apartment`.

Booking endpoints: `POST /api/bookings` creates a confirmed demo booking with `listingId`, `startDate`, and `endDate` (`YYYY-MM-DD`); `GET /api/bookings/mine` and `GET /api/bookings/received` list bookings; `PATCH /api/bookings/:id/cancel` cancels a booking owned by the signed-in user.

## Configuration

| Variable | Required | Default |
|---|---|---|
| `PORT` | No | `3000` |
| `MONGODB_URI` | No | `mongodb://127.0.0.1:27017/rental_property_management` |
| `JWT_SECRET` | Yes | None; app startup requires at least 32 characters |
| `JWT_EXPIRES_IN` | No | `1d` |

## Tests

Run `npm test`. The automated tests cover request validation and authorization boundaries without requiring a running MongoDB instance.
