# Items CRUD API

A minimal REST API for managing inventory items, built with FastAPI and
SQLAlchemy. Data is stored locally in SQLite.

## Features

- Create, read, update, and delete items
- Paginated item listing with `skip` and `limit` query parameters
- Request validation and interactive OpenAPI documentation
- File-based SQLite persistence in `items.db`

## Requirements

- Python 3.10 or later

## Setup

Create and activate a virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

On Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Install the dependencies:

```bash
python -m pip install -r requirements.txt
```

## Run the API

```bash
uvicorn main:app --reload
```

The server starts at <http://127.0.0.1:8000>. The SQLite database file
(`items.db`) and its `items` table are created automatically on startup.

## API documentation

With the server running, visit:

- Interactive Swagger UI: <http://127.0.0.1:8000/docs>
- ReDoc: <http://127.0.0.1:8000/redoc>
- OpenAPI schema: <http://127.0.0.1:8000/openapi.json>

## Endpoints

| Method | Path | Description |
| --- | --- | --- |
| `POST` | `/items` | Create an item |
| `GET` | `/items` | List items; accepts `skip` and `limit` |
| `GET` | `/items/{item_id}` | Get one item |
| `PUT` | `/items/{item_id}` | Update one or more item fields |
| `DELETE` | `/items/{item_id}` | Delete an item |

An item has the following shape:

```json
{
  "id": 1,
  "name": "Mechanical keyboard",
  "description": "Compact wireless keyboard",
  "in_stock": true
}
```

`id` is assigned by the API. `description` is optional and `in_stock`
defaults to `true` when an item is created.

## Examples

Create an item:

```bash
curl -X POST http://127.0.0.1:8000/items \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Mechanical keyboard",
    "description": "Compact wireless keyboard",
    "in_stock": true
  }'
```

List items, returning up to 20 results after the first 10:

```bash
curl "http://127.0.0.1:8000/items?skip=10&limit=20"
```

Update only the stock status of item `1`:

```bash
curl -X PUT http://127.0.0.1:8000/items/1 \
  -H "Content-Type: application/json" \
  -d '{"in_stock": false}'
```

Delete item `1`:

```bash
curl -X DELETE -i http://127.0.0.1:8000/items/1
```

A missing item returns `404` with `{"detail":"Item not found"}`. A
successful deletion returns `204 No Content`.

## Project files

```text
.
├── main.py           # FastAPI app, SQLAlchemy model, and routes
├── requirements.txt  # Python dependencies
├── readme.md         # Project documentation
└── items.db          # Local SQLite data (created at runtime)
```

## Database configuration

The connection string is defined in `main.py` as
`sqlite:///./items.db`. For a production deployment, set up a managed
database and replace that URL with the appropriate SQLAlchemy-compatible
connection string. Do not use the development SQLite configuration as-is in
a multi-instance production deployment.
