# Quran.com API (v4) — Integration & Documentation Guide

The **Quran.com API (v4)** (`https://api.quran.com/api/v4`) is the official backend service engineered by the **Quran Foundation** to power the modern [Quran.com](https://quran.com) web and mobile applications. It is renowned for its granular word-by-word linguistics, audio selector support, high-resolution font metadata, and rich Tafsir commentaries.

---

## Quick Reference Summary

| Parameter | Details |
| :--- | :--- |
| **Base URL** | `https://api.quran.com/api/v4` |
| **CDN Base URL** | `https://api.qurancdn.com/api/v4` |
| **Auth** | Free / None required |
| **Interactive Docs** | `https://api-docs.quran.foundation/` |
| **Official SDK** | `@quranjs/api` (Node.js, TypeScript, React) |
| **Key Distinctive Features** | Word-by-word audio timestamps, morphology, Tajweed glyphs, multiple Tafsirs |

---

## Response Structure

### Chapter Endpoint (`GET /chapters/1`)
```json
{
  "chapter": {
    "id": 1,
    "revelation_place": "makkah",
    "revelation_order": 5,
    "bismillah_pre": false,
    "name_simple": "Al-Fatihah",
    "name_complex": "Al-Fātiḥah",
    "name_arabic": "الفاتحة",
    "verses_count": 7,
    "pages": [1, 1],
    "translated_name": {
      "language_name": "english",
      "name": "The Opener"
    }
  }
}
```

---

## 1. Chapters (Surahs)

### 1.1 List All Chapters
* **Endpoint:** `GET /chapters`
* **Query Parameters:** `language` (default: `en`)
* **Example:**
  ```bash
  curl -X GET "https://api.quran.com/api/v4/chapters?language=en"
  ```

### 1.2 Get Chapter Details & Info
* **Metadata:** `GET /chapters/{id}`
* **Historical Background:** `GET /chapters/{id}/info` (revelation background, historical timeline, main topics)

---

## 2. Verses (Ayahs) & Word-by-Word

### 2.1 Verses by Chapter
Retrieve verses with translations, word breakdown, transliteration, and audio segments.

* **Endpoint:** `GET /verses/by_chapter/{chapter_number}`
* **Query Parameters:**
  * `language` (*string*, default: `en`)
  * `words` (*boolean*, default: `true`) — Include word-by-word analysis.
  * `translations` (*string*) — Comma-delimited resource IDs (e.g., `131,20`).
  * `tafsirs` (*string*) — Comma-delimited tafsir resource IDs.
  * `fields` (*string*) — e.g., `text_uthmani,chapter_id,verse_key`.
  * `per_page` (*integer*, default: `10`, max: `50`).
  * `page` (*integer*, default: `1`).

#### Example Request
```bash
curl -X GET "https://api.quran.com/api/v4/verses/by_chapter/1?translations=131&words=true&per_page=7"
```

### 2.2 Verses by Key
* **Endpoint:** `GET /verses/by_key/{verse_key}`
* **Example:** `GET /verses/by_key/2:255`

### 2.3 Verses by Page / Juz / Hizb
* **By Mushaf Page:** `GET /verses/by_page/{page_number}` (1 to 604)
* **By Juz:** `GET /verses/by_juz/{juz_number}` (1 to 30)
* **By Hizb:** `GET /verses/by_hizb/{hizb_number}` (1 to 60)
* **By Rub El Hizb:** `GET /verses/by_rub_el_hizb/{rub_number}` (1 to 240)

---

## 3. Resources Directory

Discover IDs for translations, tafsirs, and audio reciters:

* **List Translations:**
  `GET /resources/translations`
  * *Returns ID, name, author_name, language_name.*
  * *(e.g. ID `131` = Dr. Mustafa Khattab, The Clear Quran)*
* **List Tafsirs:**
  `GET /resources/tafsirs`
  * *(e.g. `169` = Tafsir Ibn Kathir)*
* **List Recitations:**
  `GET /resources/recitations`
  * *(e.g. ID `7` = Mishary Rashid Alafasy)*
* **List Languages:**
  `GET /resources/languages`

---

## 4. Audio & Recitations

### 4.1 Full Chapter Recitation Audio
* **Endpoint:** `GET /chapter_recitations/{reciter_id}/{chapter_number}`
* **Example:** `GET /chapter_recitations/7/1`

```json
{
  "audio_file": {
    "id": 1,
    "chapter_id": 1,
    "file_size": 842600,
    "format": "mp3",
    "audio_url": "https://download.quranicaudio.com/quran/mishaari_raashid_al_3afaasee/001.mp3",
    "duration": 48
  }
}
```

### 4.2 Verse-by-Verse Audio with Word Timestamps
* **Endpoint:** `GET /recitations/{reciter_id}/by_chapter/{chapter_number}`
* Returns verse audio files paired with millisecond start/end timestamps per word for synchronized Karaoke-style Quran reading.

---

## 5. Scholarly Tafsir (Commentary)

* **Endpoint:** `GET /tafsirs/{tafsir_id}/by_ayah/{verse_key}`
* **Example:**
  ```bash
  curl -X GET "https://api.qurancdn.com/api/v4/tafsirs/en-tafisr-ibn-kathir/by_ayah/1:1"
  ```

---

## 6. Quran Search

* **Endpoint:** `GET /search`
* **Query Parameters:**
  * `q` (*string*, required) — Keyword (e.g. `mercy`, `peace`).
  * `size` (*integer*, default: `20`).
  * `page` (*integer*, default: `1`).
  * `language` (*string*, default: `en`).

---

## Integration Code Examples

### TypeScript (Node / Web)
```typescript
interface QuranChapter {
  id: number;
  name_simple: string;
  name_arabic: string;
  verses_count: number;
}

export async function fetchAllChapters(): Promise<QuranChapter[]> {
  const res = await fetch('https://api.quran.com/api/v4/chapters');
  if (!res.ok) throw new Error(`Quran.com API error: ${res.statusText}`);
  const data = await res.json();
  return data.chapters;
}
```

### Python
```python
import requests

def get_chapter_verses(chapter_num: int, translation_id: int = 131):
    url = f"https://api.quran.com/api/v4/verses/by_chapter/{chapter_num}"
    params = {
        "translations": translation_id,
        "words": "false",
        "per_page": 10
    }
    response = requests.get(url, params=params, timeout=10)
    response.raise_for_status()
    return response.json()["verses"]

verses = get_chapter_verses(1)
for v in verses:
    print(f"[{v['verse_key']}] {v['translations'][0]['text']}")
```
