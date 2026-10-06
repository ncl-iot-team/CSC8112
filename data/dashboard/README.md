# Quality Dashboard

Requires Docker (with Docker Compose).

## Start

Put `dashboard-cloud.tar`, `docker-compose.yml` in one folder, then run in that folder:

```bash
docker load -i dashboard-cloud.tar
docker compose up -d
```

Open http://dashboard.cloud.ncl.ac.uk:8000/ in your browser to view the dashboard.

Interactive API docs: http://dashboard.cloud.ncl.ac.uk:8000/docs

## API

Base URL: `http://dashboard.cloud.ncl.ac.uk:8000`.

Record fields: `id`, `timestamp` (epoch seconds, float, required, set by you), `value` (float), `predicted_quality` (text),`uploaded_at` (UTC, ISO 8601, set by the server, optional override), `image_ext`.

1. Create (multipart form; `timestamp` is required; uploaded_at is optional and defaults to now:

```bash
curl -F timestamp=1760000000 -F value=0.82 -F predicted_quality=good -F image=@sample.jpg http://dashboard.cloud.ncl.ac.uk:8000/api/records
```

2. List (supports `offset` and `limit`, max 200):

```bash
curl "http://dashboard.cloud.ncl.ac.uk:8000/api/records?offset=0&limit=50"
```

3. Get one:

```bash
curl http://dashboard.cloud.ncl.ac.uk:8000/api/records/<id>
```

4. Update (send only the fields to change; `image` replaces the file):

```bash
curl -X PUT -F predicted_quality=bad http://dashboard.cloud.ncl.ac.uk:8000/api/records/<id>
```

5. Delete:

```bash
curl -X DELETE http://dashboard.cloud.ncl.ac.uk:8000/api/records/<id>
```

6. Download the image or thumbnail:

```bash
curl -o out.jpg http://dashboard.cloud.ncl.ac.uk:8000/api/records/<id>/image
curl -o thumb.jpg http://dashboard.cloud.ncl.ac.uk:8000/api/records/<id>/thumb
```

Image rules: JPEG, PNG or WEBP, max 10 MB.

## Python example:

```python
import requests
with open("sample.jpg", "rb") as f:
    r = requests.post("http://dashboard.cloud.ncl.ac.uk:8000/api/records",
                      data={"timestamp": 1760000000, "value": 0.82, "predicted_quality": "good"},
                      files={"image": f})
print(r.json())
```