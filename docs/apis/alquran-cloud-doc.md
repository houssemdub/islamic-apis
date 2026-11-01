# AlQuran Cloud API — Integration & Documentation Guide

The **AlQuran Cloud API** (`https://api.alquran.cloud/v1`) is an open, keyless, and feature-rich RESTful service that serves the complete Holy Quran. It provides access to original Arabic scripts, verified translations in dozens of languages, word search, structural divisions (Surahs, Juzs, Pages, Hizbs, Manzils, Rukus), and synchronized audio streaming via CDN.

---

## Quick Reference Summary

| Parameter | Details |
| :--- | :--- |
| **Base URL** | `https://api.alquran.cloud/v1` |
| **Authentication** | Free / Keyless (No signup required) |
| **Format** | JSON |
| **Coverage** | 114 Surahs, 6,236 Ayahs, 30 Juzs, 604 Pages |
| **Multi-Edition Support** | Query multiple translations/audios simultaneously using commas |
| **Compression** | Supports `Accept-Encoding: gzip, zstd` |

---

## Response Structure

```json
{
  "code": 200,
  "status": "OK",
  "data": {
    "number": 1,
    "name": "سُورَةُ ٱلْفَاتِحَةِ",
    "englishName": "Al-Faatiha",
    "englishNameTranslation": "The Opening",
    "revelationType": "Meccan",
    "numberOfAyahs": 7,
    "ayahs": [
      {
        "number": 1,
        "text": "بِسْمِ ٱللَّهِ ٱلرَّحْمَـٰنِ ٱلرَّحِيمِ",
        "numberInSurah": 1,
        "juz": 1,
        "manzil": 1,
        "page": 1,
        "ruku": 1,
        "hizbQuarter": 1,
        "sajda": false
      }
    ],
    "edition": {
      "identifier": "quran-uthmani",
      "language": "ar",
      "name": "Uthmani",
      "englishName": "Uthmani",
      "format": "text",
      "type": "quran",
      "direction": "rtl"
    }
  }
}
```

---

## 1. Surah Endpoints

### 1.1 List All Surahs
Returns high-level metadata for all 114 Surahs without the verse text (ideal for navigation drawers and indexes).

* **Endpoint:** `GET /surah`
* **cURL:**
  ```bash
  curl -X GET "https://api.alquran.cloud/v1/surah"
  ```

### 1.2 Get Surah by Number
* **Endpoint:** `GET /surah/{number}`
* **Default:** Returns Arabic Uthmani text.
* **Example:** `GET /surah/1` (Al-Fatiha), `GET /surah/36` (Ya-Sin).

### 1.3 Get Surah in a Specific Edition (or Multiple Editions)
Pass one or more comma-separated edition identifiers:

* **Endpoint:** `GET /surah/{number}/{edition}`
* **Single Edition Example:**
  ```bash
  curl -X GET "https://api.alquran.cloud/v1/surah/1/en.asad"
  ```
* **Multi-Edition Example (Arabic + English + Audio in one request):**
  ```bash
  curl -X GET "https://api.alquran.cloud/v1/surah/1/editions/quran-uthmani,en.sahih,ar.alafasy"
  ```

---

## 2. Ayah Endpoints

### 2.1 By Global Number or Surah:Ayah
You can refer to an Ayah in two ways:
1. **Absolute sequential index:** `1` to `6236`
2. **Coordinate syntax:** `{surah}:{ayah}` (e.g. `2:255` for Ayat al-Kursi)

* **Endpoints:**
  * `GET /ayah/{reference}`
  * `GET /ayah/{reference}/{edition}`
  * `GET /ayah/{reference}/editions/{editions}`

#### Example: Ayat al-Kursi in English & Arabic
```bash
curl -X GET "https://api.alquran.cloud/v1/ayah/2:255/editions/quran-uthmani,en.sahih"
```

### 2.2 Random Ayah
* **Endpoint:** `GET /ayah/random`
* **Endpoint with Edition:** `GET /ayah/random/en.sahih`

---

## 3. Structural Divisions (Juz, Page, Hizb, Manzil)

* **Juz (1 to 30):**
  `GET /juz/{number}/{edition}`
  * *Example:* `GET /v1/juz/30/en.sahih`
* **Page of Medina Mushaf (1 to 604):**
  `GET /page/{number}/{edition}`
  * *Example:* `GET /v1/page/604/quran-uthmani`
* **Hizb Quarter (1 to 240):**
  `GET /hizbQuarter/{number}/{edition}`
* **Manzil (1 to 7):**
  `GET /manzil/{number}/{edition}`

---

## 4. Editions Directory

To look up available translations, reciters, and editions:

* **List All Editions:**
  `GET /edition`
* **Filter by Format:**
  `GET /edition?format=text` or `GET /edition?format=audio`
* **Filter by Language:**
  `GET /edition/language/en`
* **Filter by Type:**
  `GET /edition/type/translation`

### Common Edition Identifiers:
* **Arabic Text:** `quran-uthmani`, `quran-simple`, `quran-tajweed`
* **English Translations:** `en.sahih` (Saheeh International), `en.asad` (Muhammad Asad), `en.pickthall` (Pickthall), `en.yusufali` (Yusuf Ali), `en.clearquran` (Dr. Mustafa Khattab)
* **Urdu Translations:** `ur.jalandhry`, `ur.ahmedali`, `ur.kanzuliman`
* **French:** `fr.hamidullah`
* **Spanish:** `es.cortes`
* **Audio Reciters (Verse-by-Verse):** `ar.alafasy`, `ar.abdulbasitmurattal`, `ar.husary`, `ar.minshawi`, `ar.hudhaify`

---

## 5. Search Endpoint

Search for matching verses by keyword.

* **Endpoint:** `GET /search/{keyword}/{surah}/{edition}`
* **Parameters:**
  * `keyword` — Query string (e.g. `mercy`, `patience`, `الله`)
  * `surah` — Specific surah number (1-114) or `all`
  * `edition` — Edition to search within (e.g. `en.sahih`, `quran-simple`)

#### Example
```bash
curl -X GET "https://api.alquran.cloud/v1/search/paradise/all/en.sahih"
```

---

## Integration Code Example

### TypeScript / Next.js
```typescript
interface Ayah {
  number: number;
  text: string;
  numberInSurah: number;
  juz: number;
  page: number;
}

interface SurahResponse {
  code: number;
  status: string;
  data: {
    number: number;
    name: string;
    englishName: string;
    ayahs: Ayah[];
  };
}

export async function getSurah(surahNumber: number, edition = 'quran-uthmani'): Promise<SurahResponse> {
  const res = await fetch(`https://api.alquran.cloud/v1/surah/${surahNumber}/${edition}`);
  if (!res.ok) {
    throw new Error(`Failed to fetch Surah ${surahNumber}`);
  }
  return res.json();
}
```

### Python
```python
import requests

def search_quran(keyword: str, edition: str = "en.sahih"):
    url = f"https://api.alquran.cloud/v1/search/{keyword}/all/{edition}"
    res = requests.get(url, timeout=10)
    res.raise_for_status()
    matches = res.json()["data"]["matches"]
    print(f"Found {len(matches)} occurrences:")
    for match in matches[:3]:
        print(f"Surah {match['surah']['englishName']} ({match['numberInSurah']}): {match['text']}")

search_quran("light")
```
