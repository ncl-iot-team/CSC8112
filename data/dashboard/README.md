# Quality Dashboard

Requires Docker (with Docker Compose).

## Start

Put `dashboard-1.0.tar`, `docker-compose.yml` in one folder, then run in that folder:

```bash
docker load -i dashboard-1.0.tar
docker compose up -d
```

Open http://localhost:8000

Interactive API docs: http://localhost:8000/docs

## Stop

```bash
docker compose down
```

Data is kept in a Docker volume. `docker compose down -v` deletes all data.

## API

Base URL: `http://localhost:8000`.

Record fields: `id`, `timestamp` (UK time, ISO 8601), `value` (float), `predicted_quality` (text), `image_ext`.

1. Create (multipart form; `timestamp` is optional and defaults to now):

```bash
curl -F value=0.82 -F predicted_quality=good -F image=@sample.jpg http://localhost:8000/api/records
```

2. List (supports `offset` and `limit`, max 200):

```bash
curl "http://localhost:8000/api/records?offset=0&limit=50"
```

3. Get one:

```bash
curl http://localhost:8000/api/records/<id>
```

4. Update (send only the fields to change; `image` replaces the file):

```bash
curl -X PUT -F predicted_quality=bad http://localhost:8000/api/records/<id>
```

5. Delete:

```bash
curl -X DELETE http://localhost:8000/api/records/<id>
```

6. Download the image or thumbnail:

```bash
curl -o out.jpg http://localhost:8000/api/records/<id>/image
curl -o thumb.jpg http://localhost:8000/api/records/<id>/thumb
```

Image rules: JPEG, PNG or WEBP, max 10 MB.

## Python example:

```python
import requests
with open("sample.jpg", "rb") as f:
    r = requests.post("http://localhost:8000/api/records",
                      data={"value": 0.82, "predicted_quality": "good"},
                      files={"image": f})
print(r.json())
```