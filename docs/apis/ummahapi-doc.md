# UmmahAPI — Integration & Documentation Guide

UmmahAPI (`https://ummahapi.com`) is a free, modern, JSON-first Islamic REST API designed to power web, mobile, and desktop applications with authentic Islamic data. It unites prayer times, complete Quran texts, word-by-word linguistics, audio recitations, canonical Hadith collections, Qibla calculations, Hijri calendar operations, Duas, and the 99 Names of Allah into a unified developer platform.

---

## Quick Reference Summary

| Property | Details |
| :--- | :--- |
| **Base URL** | `https://ummahapi.com` |
| **Auth Type** | None required for anonymous requests; Optional API Key for high volume |
| **Auth Header / Query** | `X-API-Key: <YOUR_KEY>` or `?apikey=<YOUR_KEY>` |
| **Rate Limit** | 100 req/min (Anonymous); custom high-tier for registered keys |
| **Format** | `application/json` (UTF-8) |
| **CORS** | Enabled (`Access-Control-Allow-Origin: *`) |
| **AI Protocol** | Supports MCP (Model Context Protocol) at `https://ummahapi.com/mcp` |

---

## Standard Response Structure

Every UmmahAPI endpoint returns a uniform envelope:

```json
{
  "success": true,
  "service": "prayer-times",
  "data": { ... },
  "timestamp": "2026-10-12T20:15:05.155Z",
  "api_info": {
    "sadaqah_jariah": "This API is provided as sadaqah jariah for the Muslim ummah"
  }
}
```

---

## 1. Prayer Times Endpoints

### 1.1 Get Today's Prayer Times
Calculates the exact daily prayer times using latitude and longitude coordinates.

* **Endpoint:** `GET /api/prayer-times`
* **Query Parameters:**
  * `lat` (*number*, required) — Latitude (e.g. `40.7128`).
  * `lng` (*number*, required) — Longitude (e.g. `-74.0060`).
  * `method` (*string*, optional, default: `MuslimWorldLeague`) — Calculation method (`NorthAmerica`, `MuslimWorldLeague`, `Egyptian`, `UmmAlQura`, `Karachi`, `Tehran`, `Gulf`, `Kuwait`, `Qatar`, `Singapore`, `France`, `Turkey`, `Russia`, `Dubai`, `JAKIM`, `KEMENAG`, `Morocco`, `Tunisia`, `Algeria`, etc.).
  * `madhab` (*string*, optional, default: `Shafi`) — `Shafi` (standard) or `Hanafi` (later Asr).
  * `highLatitudeRule` (*string*, optional) — `MiddleOfTheNight`, `SeventhOfTheNight`, `TwilightAngle`.

#### Example Request
```bash
curl -X GET "https://ummahapi.com/api/prayer-times?lat=40.7128&lng=-74.0060&method=NorthAmerica&madhab=Shafi"
```

#### Example Response
```json
{
  "success": true,
  "service": "prayer-times",
  "data": {
    "coordinates": { "lat": 40.7128, "lng": -74.006 },
    "date": "2026-10-12",
    "method": "NorthAmerica",
    "madhab": "Shafi",
    "times": {
      "fajr": "05:45",
      "sunrise": "07:05",
      "dhuhr": "12:47",
      "asr": "15:58",
      "maghrib": "18:22",
      "isha": "19:42"
    }
  },
  "timestamp": "2026-10-12T20:15:00.000Z"
}
```

### 1.2 Get Monthly Prayer Times
* **Endpoint:** `GET /api/prayer-times/month`
* **Query Parameters:** `lat`, `lng`, `month` (1-12), `year` (e.g. 2026), `method`, `madhab`.

### 1.3 List Calculation Methods
* **Endpoint:** `GET /api/prayer-times/methods`
* Returns all 23 supported calculation methods with default Fajr and Isha depression angles, including `Algeria` (Algerian Ministry of Religious Affairs and Wakfs: Fajr `18°`, Isha `17°`).

```bash
# Request prayer times for Algiers using Algerian Ministry calculation method
curl -X GET "https://ummahapi.com/api/prayer-times?lat=36.7538&lng=3.0588&method=Algeria&madhab=Shafi"
```

### 1.4 Ramadan Timetable
* **Endpoint:** `GET /api/ramadan/{year}`
* **Parameters:** `year` (path), `lat` (query), `lng` (query).

---

## 2. Qibla Direction Endpoint

Computes the great-circle compass bearing towards the Kaaba in Makkah.

* **Endpoint:** `GET /api/qibla`
* **Query Parameters:** `lat` (required), `lng` (required).

```bash
curl -X GET "https://ummahapi.com/api/qibla?lat=40.7128&lng=-74.0060"
```

```json
{
  "success": true,
  "service": "qibla",
  "data": {
    "direction": 58.48,
    "cardinal": "ENE",
    "distance_km": 10298.5
  }
}
```

---

## 3. Hijri Calendar & Moon Sighting

### 3.1 Today's Hijri Date
* **Endpoint:** `GET /api/today-hijri`
* Returns today's converted Islamic date, Hijri month details, day of the week, and holiday notices.

```json
{
  "success": true,
  "service": "today-hijri",
  "data": {
    "gregorian": {
      "date": "2026-10-12",
      "formatted": "Monday, October 12, 2026",
      "day": 12,
      "month": 10,
      "year": 2026
    },
    "hijri": {
      "date": "1448-05-01",
      "formatted": "01 Jumada al-awwal 1448 AH",
      "day": 1,
      "month": 5,
      "month_name": "Jumada al-awwal",
      "month_name_arabic": "جمادى الأولى",
      "year": 1448
    }
  }
}
```

### 3.2 Moon Sighting Data
* **Endpoint:** `GET /api/moon`
* Returns current lunar phase, crescent visibility calculations, and astronomical illumination.

---

## 4. Quran Endpoints

### 4.1 List All Surahs
* **Endpoint:** `GET /api/quran/surahs`
* Returns all 114 Surahs with revelation place (Meccan/Medinan), total ayahs, and English translations.

### 4.2 Get Complete Surah
* **Endpoint:** `GET /api/quran/surah/{number}`
* **Path Parameters:** `number` (1 to 114)
* **Query Parameters:** `translation` (e.g. `en.sahih`), `reciter` (e.g. `alafasy`).

### 4.3 Get Single Ayah
* **Endpoint:** `GET /api/quran/surah/{number}/ayah/{ayah}`
* **Example:** `GET /api/quran/surah/2/ayah/255` (Ayat al-Kursi).

### 4.4 Word-by-Word Morphology
* **Endpoint:** `GET /api/quran/words/{surah}/{ayah}`
* Breaks down each Arabic word with transliteration, root, and exact English meaning.

### 4.5 Quran Search
* **Endpoint:** `GET /api/quran/search?q={query}`

---

## 5. Hadith Endpoints

Retrieves canonical Hadiths with Arabic text, English translation, and narrator chains (*isnad*).

* **Endpoint:** `GET /api/hadith/{collection}/{number}`
* **Collections Supported:**
  * `bukhari` (Sahih al-Bukhari)
  * `muslim` (Sahih Muslim)
  * `abudawud` (Sunan Abi Dawud)
  * `tirmidhi` (Jami` at-Tirmidhi)
  * `nasai` (Sunan an-Nasa'i)
  * `ibnmajah` (Sunan Ibn Majah)

```bash
curl -X GET "https://ummahapi.com/api/hadith/bukhari/1"
```

---

## 6. Duas & Supplications

* **Get All Duas:** `GET /api/duas`
* **Categories:** `GET /api/duas/categories` (27 categories: morning, evening, travel, prayer, sickness, etc.)
* **Random Dua:** `GET /api/duas/random`
* **Search Duas:** `GET /api/duas/search?q={query}`

---

## 7. 99 Names of Allah (Asma ul-Husna)

* **All Names:** `GET /api/asma-ul-husna`
* **By Rank (1-99):** `GET /api/asma-ul-husna/{number}`
* **Random Name:** `GET /api/asma-ul-husna/random`

---

## 8. Tafsir Commentary

* **Endpoint:** `GET /api/tafsir/{edition}/surah/{surah}/ayah/{ayah}`
* **Supported Commentaries:**
  * `ibn_kathir` (Tafsir Ibn Kathir)
  * `maarif` (Ma'arif al-Qur'an)
  * `muyassar` (Al-Tafsir Al-Muyassar)

---

## 9. Zakat Calculator

* **Endpoint:** `POST /api/zakat/calculate`
* **Headers:** `Content-Type: application/json`
* **Body:**
```json
{
  "currency": "USD",
  "cash": 12000,
  "gold_grams": 45,
  "silver_grams": 100,
  "investments": 3000,
  "liabilities": 1500
}
```

---

## 10. AI / MCP Integration (Model Context Protocol)

UmmahAPI exposes an official MCP endpoint for LLM coding tools:
* **MCP URL:** `https://ummahapi.com/mcp`
* Compatible with Claude Desktop, Cursor, Copilot, and Antigravity for direct hallucination-free retrieval of Quran, Hadith, and Prayer data.

---

## Integration Code Examples

### JavaScript / TypeScript (Fetch)
```typescript
interface PrayerTimesResponse {
  success: boolean;
  service: string;
  data: {
    times: {
      fajr: string;
      sunrise: string;
      dhuhr: string;
      asr: string;
      maghrib: string;
      isha: string;
    };
  };
}

async function getPrayerTimes(lat: number, lng: number): Promise<PrayerTimesResponse> {
  const url = `https://ummahapi.com/api/prayer-times?lat=${lat}&lng=${lng}&method=NorthAmerica`;
  const response = await fetch(url);
  if (!response.ok) {
    throw new Error(`UmmahAPI error: ${response.statusText}`);
  }
  return response.json();
}

// Usage
getPrayerTimes(40.7128, -74.0060).then(data => console.log(data.data.times));
```

### Python (Requests)
```python
import requests

def get_today_hijri():
    response = requests.get("https://ummahapi.com/api/today-hijri", timeout=10)
    response.raise_for_status()
    payload = response.json()
    return payload["data"]["hijri"]

print(get_today_hijri())
```
