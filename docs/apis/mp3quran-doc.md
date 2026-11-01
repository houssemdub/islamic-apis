# MP3Quran.net REST API (v3) Integration Guide

Comprehensive documentation for developers integrating audio recitations, live radio broadcasts, and Islamic media streams from **MP3Quran.net**.

---

## 1. Overview & Service Capabilities

MP3Quran is the internet's most prolific Islamic audio distribution network. Its v3 REST API offers programmatic access to high-fidelity audio streams for mobile apps, web players, IoT speakers, and desktop utilities.

- **Base URL:** `https://mp3quran.net/api/v3`
- **Protocol:** HTTPS REST (JSON responses) & direct MP3 CDN links
- **Authentication:** None (Public / Keyless)
- **Rate Limit:** Open access (global edge CDN)

---

## 2. Audio URL Assembly Pattern

MP3Quran delivers audio through high-speed distributed audio servers (`server6.mp3quran.net`, `server8.mp3quran.net`, etc.). 

When you query `/reciters`, each reciter item includes an array of `moshaf` objects containing:
1. `server`: The base media server URL (e.g., `https://server8.mp3quran.net/afs/`)
2. `surah_list`: A comma-separated list of available Surahs (e.g., `"1,2,3,...,114"`)

### Constructing the Surah Audio Stream URL:
```text
{moshaf.server}{surah_number_zero_padded_to_3_digits}.mp3
```
*Example for Surah Al-Fatihah (1) by Mishary Rashid Alafasy:*
```
https://server8.mp3quran.net/afs/001.mp3
```

---

## 3. Core API Endpoints

### 3.1 List Reciters (`GET /reciters`)
Fetches all reciters with their available narrations and media servers.

```bash
curl "https://mp3quran.net/api/v3/reciters?language=eng"
```

**Query Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `language` | string | No | Response language (`ar`, `eng`, `fr`, `ru`, `tr`, etc.) |
| `reciter` | integer | No | Filter by specific reciter ID (e.g. `54`) |
| `rewaya` | integer | No | Filter by Riwaya/narration ID (e.g. `1` for Hafs 'an Asim) |

**Sample Response Schema:**
```json
{
  "reciters": [
    {
      "id": 54,
      "name": "Mishary Alafasy",
      "letter": "M",
      "date": "2020-05-10",
      "moshaf": [
        {
          "id": 1,
          "name": "Hafs A'n Assem",
          "server": "https://server8.mp3quran.net/afs/",
          "surah_total": 114,
          "moshaf_type": 0,
          "surah_list": "1,2,3,4,...,114"
        }
      ]
    }
  ]
}
```

---

### 3.2 List All Chapters / Suwar (`GET /suwar`)
Retrieves metadata for all 114 Surahs.

```bash
curl "https://mp3quran.net/api/v3/suwar?language=eng"
```

**Sample Response:**
```json
{
  "suwar": [
    {
      "id": 1,
      "name": "Al-Faatiha",
      "start_page": 1,
      "end_page": 1,
      "makkia": 1,
      "type": 0
    },
    {
      "id": 2,
      "name": "Al-Baqara",
      "start_page": 2,
      "end_page": 49,
      "makkia": 0,
      "type": 1
    }
  ]
}
```

---

### 3.3 24/7 Live Radio Stations (`GET /radios`)
Returns live streaming internet radio stations dedicated to individual reciters, translations, and specialized recitations.

```bash
curl "https://mp3quran.net/api/v3/radios?language=eng"
```

**Response Example:**
```json
{
  "radios": [
    {
      "id": 1,
      "name": "Radio Mishary Alafasy",
      "url": "https://backup.qurango.net/radio/mishary_alafasi"
    },
    {
      "id": 2,
      "name": "Radio Abdulbasit Abdulsamad",
      "url": "https://backup.qurango.net/radio/abdulbasit_abdulsamad_mojawwad"
    }
  ]
}
```

---

### 3.4 Live Holy Sanctuaries Video Streams (`GET /live-tv`)
Returns live streaming HLS / broadcast feeds from Makkah and Madinah.

```bash
curl "https://mp3quran.net/api/v3/live-tv"
```

---

## 4. Code Examples

### JavaScript / TypeScript Browser Audio Player
```typescript
interface Moshaf {
  server: string;
  surah_list: string;
}

interface Reciter {
  id: number;
  name: string;
  moshaf: Moshaf[];
}

export function getSurahAudioUrl(serverUrl: string, surahNumber: number): string {
  const padded = surahNumber.toString().padStart(3, '0');
  const base = serverUrl.endsWith('/') ? serverUrl : `${serverUrl}/`;
  return `${base}${padded}.mp3`;
}

// Example usage:
const audioUrl = getSurahAudioUrl("https://server8.mp3quran.net/afs/", 1);
const player = new Audio(audioUrl);
player.play();
```
