# StayFinder - Hotel Management & Discovery Platform

React + Redux Toolkit frontend, Express + native-SQL PostgreSQL backend (no ORM), Multer image uploads, React Leaflet / OpenStreetMap maps.

## Prerequisites
- Node.js 18+ and npm
- PostgreSQL 13+ (with `psql` / `createdb` on your PATH)

## 1. Database
```bash
createdb -U postgres hotel_management
cd backend
psql -U postgres -d hotel_management -f schema.sql
psql -U postgres -d hotel_management -f seed.sql   # 9 sample hotels
```

## 2. Backend
```bash
cd backend
cp .env.example .env      # then set DB_PASSWORD etc.
npm install
npm run dev               # or: npm start  -> http://localhost:5000
```
Check `http://localhost:5000/api/health` and `http://localhost:5000/api/hotels`.

Seed hotels use placeholder SVG images (`backend/uploads/seed-*.svg`); these are never deleted by the API. Images you upload are stored in `backend/uploads/` and removed when the hotel is deleted or the image replaced.

## 3. Frontend
```bash
cd frontend
cp .env.example .env
npm install
npm start                 # http://localhost:3000
```

## API
| Method | Endpoint | Notes |
|---|---|---|
| GET | `/api/hotels?search=&minPrice=&maxPrice=&limit=6&offset=0` | returns `data` + `pagination` (total, totalPages, currentPage) |
| GET | `/api/hotels/:id` | single hotel |
| POST | `/api/hotels` | multipart: image, title, description, latitude, longitude, price |
| PUT | `/api/hotels/:id` | multipart; image optional (old image kept if omitted) |
| DELETE | `/api/hotels/:id` | removes record and image file |

Quick test:
```bash
curl "http://localhost:5000/api/hotels?search=grand&minPrice=1000&maxPrice=15000&limit=6&offset=0"
curl -X POST http://localhost:5000/api/hotels -F title="Test Inn" -F description="Nice" \
  -F latitude=12.97 -F longitude=77.59 -F price=3500 -F image=@/path/photo.jpg
```

## Notes
- Prices are shown in INR; change `formatPrice` in `frontend/src/services/hotelService.js` for another currency.
- Routes: `/` (Discover), `/manage`, `/hotels/add`, `/hotels/:id`, `/hotels/:id/edit`.
- Security: parameterized queries only, MIME + extension + size checks on uploads, express-validator on every route, CORS restricted to `CLIENT_URL`, DB credentials only in backend `.env`.
