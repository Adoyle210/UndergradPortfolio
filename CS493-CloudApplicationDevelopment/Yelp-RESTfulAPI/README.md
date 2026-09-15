# Yelp-Style Business Review API

A RESTful API for a Yelp-like application, built with Node.js and Express. Supports businesses, reviews, and photos, backed by static in-memory JSON data, and packaged with Docker.

## Endpoints implemented

### Businesses
- `GET /businesses` — paginated list of all businesses (10 per page), with `nextPage`/`prevPage`/`lastPage` links.
- `GET /businesses/:id` — details for one business, including its reviews and photos.
- `POST /businesses` — create a business. Requires `name`, `address`, `city`, `state`, `zip`, `phone`, `category`, `subcategory`; `website` and `email` are optional.
- `PUT /businesses/:id` — update any field of an existing business.
- `DELETE /businesses/:id` — remove a business.
- `GET /businesses/user/:ownerid` — list businesses owned by a given user.

### Reviews
- `GET /reviews` — paginated list of all reviews.
- `POST /reviews` — create a review. Requires `userid`, `businessid`, `stars`, `dollars`; `review` text is optional.
- `PUT /reviews/:userid/:businessid` — update a review's stars, dollar rating, or text.
- `DELETE /reviews/:userid/:businessid` — remove a review.
- `GET /reviews/user/:userid` — list reviews written by a given user.

### Photos
- `GET /photos` — paginated list of all photos.
- `POST /photos/:userid/:businessid` — upload a photo (with optional caption) for a business.
- `PUT /photos/:userid/:businessid` — update a photo's caption.
- `DELETE /photos/:userid/:businessid` — remove a photo.
- `GET /photos/user/:userid` — list photos uploaded by a given user.

### Errors
- Any unmatched route returns a `404` with a JSON error body.

## Data

Bootstrapped from static JSON files in `data/` (`businesses.json`, `photos.json`, `reviews.json`), loaded with `require()`. Data lives in memory only — nothing is written back to disk, and changes reset when the server restarts.

## Testing

A Postman collection (`ASSIGN 1.postman_collection.json`) is included, with a sample request for each endpoint above.

## Running the server

```bash
npm install
npm start          # starts on the port in $PORT, default 8080
# or, for auto-restart on file changes:
npm run dev
```

## Running with Docker

```bash
docker build -f dockerfile -t yelp-api .
docker run -p 8080:8080 yelp-api
```

> Note: the Dockerfile in this repo is named `dockerfile` (lowercase), so the `-f dockerfile` flag above is required on case-sensitive filesystems (Linux). Renaming it to `Dockerfile` lets you drop the flag and use plain `docker build .`.

## Known limitations

- `POST /photos/:userid/:businessid` returns a `201` with the new photo object, but doesn't actually push it into the in-memory `photos` array — so a newly "created" photo won't show up in a later `GET /photos` or `GET /photos/user/:userid`.
- A few POST/PUT handlers (e.g. `POST /businesses`, `POST /reviews`) don't `return` immediately after sending a response inside a validation branch, so it's possible for more than one `res.send()`/`res.status()` call to run for a single request in some edge cases.
- Pagination links are included on list endpoints, but individual resource responses don't include HATEOAS links to related resources (e.g. a business's reviews/photos as links rather than embedded arrays).
- No OpenAPI/Swagger specification is included (this was only required for the grad-section version of the assignment).