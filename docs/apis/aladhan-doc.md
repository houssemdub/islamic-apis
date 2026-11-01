# AlAdhan Prayer Times API — Integration & Documentation Guide

The **AlAdhan Prayer Times API** (`https://api.aladhan.com/v1`) is the industry benchmark for calculating Islamic prayer times, Hijri calendars, and Qibla bearings. Built and maintained under the Islamic Network initiative, it is completely free, keyless, and serves millions of daily requests across mobile and web platforms globally.

---

## Quick Reference Summary

| Parameter | Value |
| :--- | :--- |
| **Base URL** | `https://api.aladhan.com/v1` |
| **Authentication** | Free / None (No API key needed) |
| **Format** | JSON |
| **Methods Supported** | 20+ Official Calculation Authorities + Custom Angles |
| **Resolution Modes** | Coordinates (`latitude`/`longitude`), `city` + `country`, or `address` |
| **CORS** | Enabled |

---

## Response Envelope Structure

```json
{
  "code": 200,
  "status": "OK",
  "data": {
    "timings": {
      "Fajr": "05:28",
      "Sunrise": "07:19",
      "Dhuhr": "12:47",
      "Asr": "15:39",
      "Sunset": "18:14",
      "Maghrib": "18:14",
      "Isha": "19:58",
      "Imsak": "05:18",
      "Midnight": "00:46",
      "Firstthird": "22:36",
      "Lastthird": "02:57"
    },
    "date": {
      "readable": "12 Oct 2026",
      "timestamp": "1791784800",
      "hijri": {
        "date": "01-05-1448",
        "format": "DD-MM-YYYY",
        "day": "1",
        "weekday": { "en": "Al Ithnayn", "ar": "الاثنين" },
        "month": { "number": 5, "en": "Jumādá al-ūlá", "ar": "جُمَادَى الأُولَى" },
        "year": "1448",
        "designation": { "abbreviated": "AH", "expanded": "Anno Hegirae" },
        "holidays": []
      },
      "gregorian": {
        "date": "12-10-2026",
        "format": "DD-MM-YYYY",
        "day": "12",
        "weekday": { "en": "Monday" },
        "month": { "number": 10, "en": "October" },
        "year": "2026"
      }
    },
    "meta": {
      "latitude": 51.508515,
      "longitude": -0.1254872,
      "timezone": "Europe/London",
      "method": {
        "id": 3,
        "name": "Muslim World League",
        "params": { "Fajr": 18, "Isha": 17 }
      },
      "latitudeAdjustmentMethod": "ANGLE_BASED",
      "midnightMode": "STANDARD",
      "school": "STANDARD",
      "offset": {
        "Imsak": 0, "Fajr": 0, "Sunrise": 0, "Dhuhr": 0,
        "Asr": 0, "Maghrib": 0, "Sunset": 0, "Isha": 0, "Midnight": 0
      }
    }
  }
}
```

---

## 1. Single Day Prayer Times

### 1.1 By Geographic Coordinates
* **Endpoint:** `GET /timings/{date}` (or `GET /timings` for today)
* **Parameters:**
  * `latitude` (*number*, required) — e.g. `51.508515`
  * `longitude` (*number*, required) — e.g. `-0.1254872`
  * `method` (*integer*, optional) — ID of method (see list below, default: `3` MWL)
  * `shafaq` (*string*, optional) — `general`, `ahmer`, `abyad` (for Isha Twilight in Shafaq calculation)
  * `tune` (*string*, optional) — Comma-delimited integer minutes to adjust prayers: `Imsak,Fajr,Sunrise,Dhuhr,Asr,Maghrib,Sunset,Isha,Midnight`
  * `school` (*integer*, optional) — `0` for Shafi/Hanbali/Maliki, `1` for Hanafi (later Asr)
  * `midnightMode` (*integer*, optional) — `0` for Standard (Sunset to Sunrise), `1` for Jafari (Sunset to Fajr)
  * `timezonestring` (*string*, optional) — e.g. `Europe/London`
  * `latitudeAdjustmentMethod` (*integer*, optional) — `1` (Middle of Night), `2` (One Seventh), `3` (Angle Based)
  * `iso8601` (*boolean*, optional) — Set to `true` to return full ISO-8601 timestamps

#### Example
```bash
curl -X GET "https://api.aladhan.com/v1/timings?latitude=51.508515&longitude=-0.1254872&method=3"
```

### 1.2 By City and Country
* **Endpoint:** `GET /timingsByCity/{date}` (or `GET /timingsByCity`)
* **Parameters:**
  * `city` (*string*, required) — e.g. `London`, `Dubai`, `Cairo`, `Jakarta`
  * `country` (*string*, required) — e.g. `United Kingdom`, `AE`, `Egypt`
  * `state` (*string*, optional) — e.g. `California`

#### Example
```bash
curl -X GET "https://api.aladhan.com/v1/timingsByCity?city=Cairo&country=Egypt&method=5"

# Algerian Ministry of Religious Affairs (method 19): Algiers, Algeria
curl -X GET "https://api.aladhan.com/v1/timingsByCity?city=Algiers&country=Algeria&method=19"
```

### 1.3 By Free-Form Address
* **Endpoint:** `GET /timingsByAddress/{date}` (or `GET /timingsByAddress`)
* **Parameters:**
  * `address` (*string*, required) — e.g. `Times Square, New York, NY`

---

## 2. Monthly & Annual Calendars

### 2.1 Calendar by City
* **Endpoint:** `GET /calendarByCity/{year}/{month}`
* **Example:** `https://api.aladhan.com/v1/calendarByCity/2026/10?city=Istanbul&country=Turkey&method=13`

### 2.2 Calendar by Coordinates
* **Endpoint:** `GET /calendar/{year}/{month}`
* **Query Parameters:** `latitude`, `longitude`, `method`, `annual=true|false`

---

## 3. Date Conversions & Islamic Calendar

### 3.1 Gregorian to Hijri
* **Endpoint:** `GET /gToH/{date}`
* **Format:** `DD-MM-YYYY`
```bash
curl -X GET "https://api.aladhan.com/v1/gToH/12-10-2026"
```

### 3.2 Hijri to Gregorian
* **Endpoint:** `GET /hToG/{date}`
* **Format:** `DD-MM-YYYY`
```bash
curl -X GET "https://api.aladhan.com/v1/hToG/01-05-1448"
```

### 3.3 Islamic Holidays for Year
* **Endpoint:** `GET /islamicHolidays/{year}`
* Returns dates for Ramadan, Eid al-Fitr, Eid al-Adha, Day of Arafah, Islamic New Year, etc.

---

## 4. Qibla Direction

Calculates the great-circle direction to the Kaaba in Mecca from coordinates.

* **Endpoint:** `GET /qibla/{latitude}/{longitude}`
* **Example:** `https://api.aladhan.com/v1/qibla/51.508515/-0.1254872`

```json
{
  "code": 200,
  "status": "OK",
  "data": {
    "latitude": 51.508515,
    "longitude": -0.1254872,
    "direction": 118.98701979929
  }
}
```

---

## 5. Calculation Methods Reference

| ID | Name | Region / Authority |
| :--- | :--- | :--- |
| `1` | University of Islamic Sciences, Karachi | Pakistan, India, Bangladesh |
| `2` | Islamic Society of North America (ISNA) | USA, Canada |
| `3` | Muslim World League (MWL) | Europe, Global standard |
| `4` | Umm Al-Qura University, Makkah | Saudi Arabia, Arabian Peninsula |
| `5` | Egyptian General Authority of Survey | Egypt, Africa, Middle East |
| `7` | Institute of Geophysics, University of Tehran | Iran, Shia communities |
| `8` | Gulf Region | Gulf states |
| `9` | Kuwait | Kuwait |
| `10` | Qatar | Qatar |
| `11` | Majlis Ugama Islam Singapura (MUIS) | Singapore |
| `12` | Union Organization Islamic de France (UOIF) | France |
| `13` | Diyanet İşleri Başkanlığı | Turkey |
| `14` | Spiritual Administration of Muslims of Russia | Russia |
| `15` | Moonsighting Committee Worldwide | UK, International |
| `16` | Dubai (unofficial) | United Arab Emirates |
| `17` | Jabatan Kemajuan Islam Malaysia (JAKIM) | Malaysia |
| `18` | Tunisian Ministry of Religious Affairs | Tunisia (Fajr: 18°, Isha: 18°) |
| `19` | Algerian Ministry of Religious Affairs and Wakfs | Algeria (Fajr: 18°, Isha: 17°) |
| `20` | KEMENAG | Indonesia (Fajr: 20°, Isha: 18°) |
| `21` | Morocco | Ministry of Habous (Fajr: 19°, Isha: 17°) |
| `22` | Comunidade Islamica de Lisboa | Portugal (Fajr: 18°, Isha: 77 min) |
| `23` | Ministry of Awqaf, Islamic Affairs and Holy Places, Jordan | Jordan (Fajr: 18°, Isha: 18°) |
| `99` | Custom Method | User-defined angles via `methodSettings` |

> **Algerian Method Specification:**
> * **Authority:** Algerian Ministry of Religious Affairs and Wakfs (*وزارة الشؤون الدينية والأوقاف*)
> * **Fajr Twilight Angle:** `18.0°`
> * **Isha Twilight Angle:** `17.0°`
> * **Juristic School (Asr):** Standard / Shafi / Maliki (`school=0`)
> * **API Usage:** Set `method=19` in AlAdhan query parameters (e.g. coordinates or `city=Algiers&country=Algeria`).

---

## Integration Code Examples

### TypeScript (Axios / Fetch)
```typescript
interface AlAdhanResponse {
  code: number;
  status: string;
  data: {
    timings: Record<string, string>;
    date: {
      readable: string;
      hijri: { date: string; month: { en: string; ar: string } };
    };
  };
}

async function fetchPrayerTimes(city: string, country: string): Promise<AlAdhanResponse> {
  const url = `https://api.aladhan.com/v1/timingsByCity?city=${encodeURIComponent(city)}&country=${encodeURIComponent(country)}&method=3`;
  const res = await fetch(url);
  if (!res.ok) throw new Error(`AlAdhan failed with status ${res.status}`);
  return res.json();
}
```

### Python
```python
import requests

def get_prayer_timings(latitude: float, longitude: float):
    url = "https://api.aladhan.com/v1/timings"
    params = {
        "latitude": latitude,
        "longitude": longitude,
        "method": 3,
        "school": 0
    }
    response = requests.get(url, params=params, timeout=10)
    response.raise_for_status()
    return response.json()["data"]["timings"]

print(get_prayer_timings(51.5085, -0.1254))
```
