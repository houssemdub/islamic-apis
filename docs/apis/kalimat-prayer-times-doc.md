# Kalimat Prayer Times API — Integration & Documentation Guide

The **Kalimat Prayer Times API (v2.0.0)** (`https://api.kalimat.dev/api/v2`) provides high-availability, enterprise-grade Islamic prayer time calculations. Trusted by tier-one apps and institutions, Kalimat does not simply apply raw mathematical estimations over sparse grids; instead, it uses coordinates and ISO country codes to resolve the closest recognized city, returning timetables calibrated to that municipality's local time zone.

---

## Quick Reference Summary

| Parameter | Details |
| :--- | :--- |
| **Base URL** | `https://api.kalimat.dev/api/v2` |
| **Endpoint** | `GET /prayer-times` |
| **Authentication** | API Key required |
| **Header** | `X-Api-Key: <YOUR_KEY>` |
| **Request Access** | Contact `info@kalimat.dev` |
| **Resolution Mode** | Nearest-city geocoding with verified municipality schedules |
| **Output Format** | JSON (v2.0 Schema) |

---

## Authentication

Every request to Kalimat requires your API key passed via HTTP headers:

```http
GET /api/v2/prayer-times?lat=24.65&lng=46.71&country_code=SA HTTP/1.1
Host: api.kalimat.dev
X-Api-Key: your_api_key_here
```

---

## Endpoint: `GET /prayer-times`

### Query Parameters

| Parameter | Type | Required | Example | Description |
| :--- | :--- | :--- | :--- | :--- |
| `lat` | `number` | **Yes** | `24.65` | Latitude coordinate |
| `lng` | `number` | **Yes** | `46.71` | Longitude coordinate |
| `country_code` | `string` | **Yes** | `SA` | ISO 3166-1 alpha-2 country code (e.g., `SA`, `US`, `AE`, `EG`, `GB`) |
| `date` | `string` | No | `2026-10-12` | Single Gregorian date (`YYYY-MM-DD`). Mutually exclusive with date ranges. Omit to fetch **today**. |
| `start_date` | `string` | No | `2026-10-01` | Range start date (`YYYY-MM-DD`). Must be provided with `end_date`. |
| `end_date` | `string` | No | `2026-10-31` | Range end date (`YYYY-MM-DD`). Must be provided with `start_date`. |

> [!NOTE]
> Do not mix single `date` with `start_date` / `end_date`. If you omit all date parameters, Kalimat returns today's schedule automatically (`defaultedToToday: true`).

---

## Response Structure

```json
{
  "data": {
    "matchedLocation": {
      "name": "Saudi Arabia, Riyadh",
      "countryCode": "SA",
      "timezone": "Asia/Riyadh",
      "latitude": 24.65,
      "longitude": 46.71,
      "distanceKm": 7.87
    },
    "period": {
      "startDate": "2026-10-12",
      "endDate": "2026-10-12",
      "defaultedToToday": true
    },
    "days": [
      {
        "date": "2026-10-12",
        "times": {
          "fajr": "04:31",
          "sunrise": "05:48",
          "dhuhr": "11:42",
          "asr": "15:02",
          "maghrib": "17:35",
          "isha": "19:05"
        }
      }
    ]
  },
  "meta": {
    "version": "2.0"
  }
}
```

---

## Code Examples

### cURL
```bash
curl -X GET "https://api.kalimat.dev/api/v2/prayer-times?lat=24.65&lng=46.71&country_code=SA" \
     -H "X-Api-Key: YOUR_API_KEY"
```

### TypeScript (Axios / Fetch)
```typescript
interface KalimatPrayerResponse {
  data: {
    matchedLocation: {
      name: string;
      countryCode: string;
      timezone: string;
      distanceKm: number;
    };
    days: Array<{
      date: string;
      times: {
        fajr: string;
        sunrise: string;
        dhuhr: string;
        asr: string;
        maghrib: string;
        isha: string;
      };
    }>;
  };
  meta: { version: string };
}

async function fetchKalimatPrayerTimes(
  lat: number,
  lng: number,
  countryCode: string,
  apiKey: string
): Promise<KalimatPrayerResponse> {
  const url = `https://api.kalimat.dev/api/v2/prayer-times?lat=${lat}&lng=${lng}&country_code=${countryCode}`;
  const response = await fetch(url, {
    headers: {
      'X-Api-Key': apiKey,
    },
  });

  if (!response.ok) {
    throw new Error(`Kalimat API Error: ${response.status} ${response.statusText}`);
  }

  return response.json();
}
```

### Python
```python
import os
import requests

def get_kalimat_prayer_times(lat: float, lng: float, country_code: str):
    api_key = os.getenv("KALIMAT_API_KEY", "YOUR_KEY_HERE")
    url = "https://api.kalimat.dev/api/v2/prayer-times"
    headers = {"X-Api-Key": api_key}
    params = {
        "lat": lat,
        "lng": lng,
        "country_code": country_code
    }
    
    resp = requests.get(url, headers=headers, params=params, timeout=10)
    resp.raise_for_status()
    payload = resp.json()
    
    matched = payload["data"]["matchedLocation"]
    print(f"Matched city: {matched['name']} ({matched['distanceKm']} km away)")
    return payload["data"]["days"][0]["times"]

# Example: Riyadh
print(get_kalimat_prayer_times(24.65, 46.71, "SA"))
```

---

## Enterprise Advantages

1. **Nearest-City Geolocation Resolution:** Rather than performing simplistic spherical math that might yield slight deviations from local Islamic councils, Kalimat identifies the nearest officially charted city and adheres to its authorized timetable.
2. **Built-in Range Queries:** Easily query full weeks or months (`start_date` and `end_date`) in a single network request to populate offline database caches.
3. **Auditability & Accuracy:** Dedicated version endpoint (`GET /api/v2/prayer-times/version`) ensures compliance tracking across regulated deployments.
