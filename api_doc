# 1C Integration API Documentation

## Base Path

All 1C endpoints are served under:

`/api/v1/1c`

## Authentication and Access

Each request must pass:

- Header: `X-API-Key: <1C app key>`
- IP whitelist check: client IP must exist in `integration_1c.allowed_ips`

`X-API-Key` source:

1. `integration_1c.app_key` from `config.yml`
2. If empty, fallback from DB table `integration_keys` where `app_name = '1C'`

If key is invalid or IP is not allowed, API returns `401 Unauthorized`.

## Rate Limit

- Scope: `IP + endpoint`
- Window: `1 hour`
- Default limit: `10 requests/hour`
- Configurable via: `integration_1c.rate_limit_per_hour`

If exceeded, API returns `429 Too Many Requests`.

## Upstream Proxy Behavior

- Requests are proxied to `integration_1c.upstream_base_url`
- Query params (`fromDate`, `toDate`) are forwarded as-is
- Upstream HTTP status and response body are returned as-is

## Cache Fallback on Upstream 429

- Cache key: `IP + endpoint + query string`
- Non-429 upstream responses are cached
- If upstream returns `429`:
  - cached response exists: return cached response
  - no cache exists: return upstream `429` response

## Endpoints

### 1) Receipts

- Method: `GET`
- Path: `/api/v1/1c/receipts1C`
- Query params:
  - `fromDate` (milliseconds timestamp)
  - `toDate` (milliseconds timestamp)

Example request:

```bash
curl -X GET 'http://localhost:2010/api/v1/1c/receipts1C?fromDate=1738627200000&toDate=1738713599000' \
  -H 'X-API-Key: <1c_app_key>'
```

Success response example:

```json
{
  "code": 0,
  "message": "OK",
  "data": [
    {
      "id": 123,
      "type": 1,
      "created_date": "2025-02-04 12:34:56",
      "cashier": "John",
      "customer": "Customer",
      "branch": "Branch A",
      "note": "",
      "is_ofd": true,
      "items": [
        {
          "id": 1,
          "name": "Item",
          "sku": "10001",
          "item_code": "",
          "articul": "10001",
          "qty": 2,
          "cost": 1000,
          "price": 1200,
          "total": 2400
        }
      ],
      "payments": [
        { "value": 2400, "type": 1 }
      ],
      "total": 2400,
      "total_payments": 2400,
      "discount": 0
    }
  ]
}
```

### 2) Purchase Orders

- Method: `GET`
- Path: `/api/v1/1c/purchOrders1C`
- Query params:
  - `fromDate` (milliseconds timestamp)
  - `toDate` (milliseconds timestamp)

Example request:

```bash
curl -X GET 'http://localhost:2010/api/v1/1c/purchOrders1C?fromDate=1738627200000&toDate=1738713599000' \
  -H 'X-API-Key: <1c_app_key>'
```

Success response example:

```json
{
  "code": 0,
  "message": "OK",
  "data": [
    {
      "id": 10,
      "status": 1,
      "supplier_name": "Supplier",
      "supplier_inn": "123456789",
      "note": "",
      "branch_name": "Branch A",
      "branch_inn": "987654321",
      "created_date": "2025-02-04 12:00:00",
      "type": 0,
      "items": [
        {
          "id": 1,
          "item": 101,
          "name": "Item",
          "sku": "10001",
          "item_code": "",
          "articul": "10001",
          "qty": 5,
          "cost": 950
        }
      ]
    }
  ]
}
```

### 3) Transfer Orders

- Method: `GET`
- Path: `/api/v1/1c/transferOrders1C`
- Query params:
  - `fromDate` (milliseconds timestamp)
  - `toDate` (milliseconds timestamp)

Example request:

```bash
curl -X GET 'http://localhost:2010/api/v1/1c/transferOrders1C?fromDate=1738627200000&toDate=1738713599000' \
  -H 'X-API-Key: <1c_app_key>'
```

Success response example:

```json
{
  "code": 0,
  "message": "OK",
  "data": [
    {
      "id": 20,
      "note": "",
      "from_branch": "Branch A",
      "to_branch": "Branch B",
      "status": 0,
      "created_date": "2025-02-04 12:10:00",
      "items": [
        {
          "id": 1,
          "item": 101,
          "name": "Item",
          "sku": "10001",
          "item_code": "",
          "articul": "10001",
          "cost": 900,
          "price": 1100,
          "qty": 3
        }
      ]
    }
  ]
}
```

## Common Error Responses

### 401 Unauthorized

- Invalid `X-API-Key`
- IP is not in whitelist

Example:

```json
{
  "code": 401,
  "message": "invalid api key"
}
```

or

```json
{
  "code": 401,
  "message": "ip not allowed"
}
```

### 429 Too Many Requests

- Local gateway rate limit exceeded (`integration_1c.rate_limit_per_hour`)
- Or upstream returned `429` and cache was not available for that key

Example:

```json
{
  "code": 429,
  "message": "rate limit exceeded"
}
```

## Configuration

`config/config.yml`:

```yml
integration_1c:
  app_key: "your_1c_app_key"
  allowed_ips:
    - "203.0.113.10"
    - "203.0.113.11"
  rate_limit_per_hour: 10
  upstream_base_url: "https://upstream.example.com"
```
