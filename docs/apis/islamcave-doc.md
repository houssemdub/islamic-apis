# IslamCave Prayer Times API — Integration & Documentation Guide

The **IslamCave Prayer Times API** (`https://islamcave.com/api/prayer-times`) is a lightweight, zero-overhead REST service designed for developers who need an immediate, dependable way to obtain all 5 daily prayers, sunrise, and Qibla angle from latitude and longitude coordinates.

---

## Quick Reference Summary

| Parameter | Details |
| :--- | :--- |
| **Base URL** | `https://islamcave.com/api` |
| **Endpoint** | `GET /prayer-times` |
| **Authentication** | None (No API key needed) |
| **Required Parameter** | `attribution=islamcave.com` |
| **Format** | `application/json` |
| **Payload Highlights** | Includes both ISO-8601 UTC timestamps & pre-formatted 12-hour AM/PM times + Qibla bearing |

---

## Endpoint Specification

### `GET /prayer-times`

#### Query Parameters

| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `latitude` | `number` (float) | **Yes** | — | Geographic latitude (e.g., `40.7128`) |
| `longitude` | `number` (float) | **Yes** | — | Geographic longitude (e.g., `-74.0060`) |
| `attribution` | `string` | **Yes** | — | Must be set to `islamcave.com` |

---

## Request Examples

### cURL
```bash
curl -X GET "https://islamcave.com/api/prayer-times?latitude=40.7128&longitude=-74.0060&attribution=islamcave.com"
```

### JavaScript / TypeScript (Fetch)
```typescript
interface IslamCaveResponse {
  status: string;
  data: {
    coordinates: { latitude: number; longitude: number };
    date: string;
    method: string;
    madhab: string;
    times: {
      fajr: string;
      sunrise: string;
      dhuhr: string;
      asr: string;
      maghrib: string;
      isha: string;
    };
    formatted: {
      fajr: string;
      sunrise: string;
      dhuhr: string;
      asr: string;
      maghrib: string;
      isha: string;
    };
    qibla: number;
  };
  attribution: {
    required: boolean;
    text: string;
    link: string;
  };
}

async function getPrayerTimesAndQibla(lat: number, lng: number): Promise<IslamCaveResponse> {
  const url = `https://islamcave.com/api/prayer-times?latitude=${lat}&longitude=${lng}&attribution=islamcave.com`;
  const res = await fetch(url);
  if (!res.ok) throw new Error(`IslamCave request failed with code ${res.status}`);
  return res.json();
}
```

### Python
```python
import requests

def fetch_islamcave_times(lat: float, lng: float):
    url = "https://islamcave.com/api/prayer-times"
    params = {
        "latitude": lat,
        "longitude": lng,
        "attribution": "islamcave.com"
    }
    response = requests.get(url, params=params, timeout=10)
    response.raise_for_status()
    return response.json()

data = fetch_islamcave_times(40.7128, -74.0060)
print("Formatted:", data["data"]["formatted"])
print("Qibla angle:", data["data"]["qibla"])
```

---

## Response Structure

```json
{
  "status": "success",
  "data": {
    "coordinates": {
      "latitude": 40.7128,
      "longitude": -74.006
    },
    "date": "2026-10-12",
    "method": "MWL",
    "madhab": "shafi",
    "times": {
      "fajr": "2026-10-12T09:33:00.000Z",
      "sunrise": "2026-10-12T11:04:00.000Z",
      "dhuhr": "2026-10-12T16:43:00.000Z",
      "asr": "2026-10-12T19:49:00.000Z",
      "maghrib": "2026-10-12T22:20:00.000Z",
      "isha": "2026-10-12T23:46:00.000Z"
    },
    "formatted": {
      "fajr": "9:33 AM",
      "sunrise": "11:04 AM",
      "dhuhr": "4:43 PM",
      "asr": "7:49 PM",
      "maghrib": "10:20 PM",
      "isha": "11:46 PM"
    },
    "qibla": 58.48169650089118
  },
  "attribution": {
    "required": true,
    "text": "Powered by IslamCave",
    "link": "https://islamcave.com",
    "message": "Thank you for attributing IslamCave!"
  },
  "meta": {
    "timestamp": "2026-10-12T20:46:13.874Z",
    "version": "1.0",
    "documentation": "https://islamcave.com/developers/api"
  }
}
```

---

## When to Choose IslamCave

* **Simplicity & Speed:** Ideal when you need a single clean GET endpoint that provides both prayer times and Qibla heading in one round-trip.
* **Pre-formatted Times:** Includes ready-to-display 12-hour localized strings (`formatted.fajr: "9:33 AM"`), eliminating tedious date formatting on resource-constrained devices (smart watches, IoT, embedded displays).
* **ISO-8601 Timestamps:** `times.*` provides exact UTC strings, making it trivial to configure client-side notification triggers and countdown timers.
