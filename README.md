Workshop-1: Products API with Caching

A small Express 5 application built for a caching workshop. It exposes two read-only endpoints for products and uses a simple in-memory cache so that repeated requests skip a slow (simulated) database read.

Features
GET /products returns all products
GET /products/:id returns a single product, or 404 if it does not exist
In-memory caching on both endpoints, keyed by the request URL
Simulated database latency of 1.5 seconds, so the effect of caching is easy to see
Products stored in a local JSON file (db.json)
Project Structure
.
├── server.js        # Express app, routes and caching logic
├── db.json          # Product data (acts as the database)
├── package.json
└── package-lock.json
Getting Started

Prerequisites: Node.js 18 or newer

bash
# Install dependencies
npm install

# Start the server
npm start

# Or start with auto-reload (nodemon)
npm run server

The server runs at http://localhost:3000.

API Reference
Method	Endpoint	Description	Responses
GET	/products	List all products	200 with a JSON array
GET	/products/:id	Get a product by its id	200 with a JSON object, 404 if not found
Product data

Products live in db.json:

json
[
  { "id": 1, "name": "Keyboard", "price": 49.99 },
  { "id": 2, "name": "Mouse", "price": 19.99 },
  { "id": 3, "name": "Monitor", "price": 199 },
  { "id": 4, "name": "Mouse", "price": 19 }
]
Examples
bash
# All products
curl http://localhost:3000/products

# One product
curl http://localhost:3000/products/1

# A product that does not exist (returns 404, "Product not found")
curl -i http://localhost:3000/products/999
How Caching Works

The cache is a plain JavaScript object in server.js. The key is the request URL (for example /products or /products/1).

A request comes in and the cache is checked using the URL as the key.
Cache hit: if a value exists, it is returned immediately.
Cache miss: the data is read from db.json (after a 1.5 second simulated delay), saved in the cache, and then returned.

You can see the difference by timing two identical requests:

bash
curl -w "%{time_total}s\n" -o /dev/null -s http://localhost:3000/products   # ~1.5s (miss)
curl -w "%{time_total}s\n" -o /dev/null -s http://localhost:3000/products   # ~0.00s (hit)
Known Limitations

This project is a workshop exercise, so some things are deliberately simple:

No expiry: cached entries never expire. Restart the server to clear the cache.
No invalidation: there are no write endpoints yet. If db.json is edited by hand, the server keeps serving the old cached data until it restarts.
/products/:id caching bug: this route caches the entire products array under the /products/:id URL instead of the single product. The first request for an id returns the correct product, but repeat requests for the same id return the whole array. A fix is to cache the matching product (cache[key] = product) rather than products.
Not cached on 404: requests for missing products are not cached, so they always hit the database.
In-memory only: the cache is lost on restart and is not shared between multiple server instances.
Errors: if reading db.json fails, the error is only logged and the request is left without a response.
Tech Stack
Node.js
Express 5
nodemon (development)
