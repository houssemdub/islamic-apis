# Kalimat Search API — Integration & Documentation Guide

The **Kalimat Search API (v2.0.0)** (`https://api.kalimat.dev/api/v2`) provides AI-driven semantic and conceptual search capabilities across primary Islamic texts: the Holy Quran, the canonical Hadith collections (*Kutub al-Sittah*), and authentic Athkar/Duas across 130+ languages. Unlike simple keyword/regex matching, Kalimat understands thematic meaning, semantic relevance, multilingual intent, and scholarly grading.

---

## Quick Reference Summary

| Parameter | Details |
| :--- | :--- |
| **Base URL** | `https://api.kalimat.dev/api/v2` |
| **Endpoint** | `GET /search` and `GET /quick-search` |
| **Authentication** | API Key required |
| **Header** | `X-Api-Key: <YOUR_KEY>` |
| **Repository Targets** | `quran`, `sunnah`, `azkar` |
| **Translations** | 130+ languages supported |
| **Hadith Filters** | Source books (`bukhari`, `muslim`, etc.), Grades (`sahih`, `hasan`, etc.) |

---

## Authentication

Supply your API key via the HTTP header on all requests:

```http
GET /api/v2/search?query=kindness+to+parents&contentType=quran&getText=true HTTP/1.1
Host: api.kalimat.dev
X-Api-Key: your_api_key_here
```

---

## 1. Semantic Search: `GET /search`

### Query Parameters

| Parameter | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `query` | `string` | **Yes** | — | Natural language question, concept, or reference (e.g. `patience in grief`, `charity in secret`) |
| `contentType` | `string` | No | `quran` | Target database: `quran`, `sunnah`, or `azkar` |
| `getText` | `boolean` | No | `false` | When `true`, returns full Arabic text and translated text with keyword highlights |
| `getMetadata` | `boolean` | No | `false` | When `true` and `contentType=sunnah`, returns `matnAr`, `isnadAr`, `gradeAr`, `sourceBook`, and `chapterNumber` |
| `userLang` | `string` | No | `""` | ISO 639-1 language code for translation (e.g. `en`, `fr`, `ur`, `id`, `tr`) |
| `numResults` | `integer` | No | `20` | Max results per page (1 to 50) |
| `start` | `integer` | No | `0` | Offset for pagination |
| `hadithSourceBook` | `string` | No | — | Filter Hadiths by collection: `bukhari`, `muslim`, `abudaud`, `tirmizi`, `nesai`, `ibnmaja` (comma-separated) |
| `hadithGrade` | `string` | No | — | Filter by authenticity: `sahih`, `hasan`, `daif`, `munkar`, `maudu`, `other` |
| `hadithChapter` | `integer` | No | — | Filter by specific chapter inside a single source book |

---

## 2. Quick Search (Autocomplete): `GET /quick-search`

Optimized for instant interactive search bars (latency < 50ms):

* **Endpoint:** `GET /api/v2/quick-search`
* **Query Parameters:**
  * `query` (*string*, required) — e.g. `Ayat al-kursi`, `Surah 18`, `Bukhari 1`
  * `getText` (*boolean*, default: `true`)
  * `contentType` (*string*, default: `quran`)

---

## Sample Response: Semantic Search

```json
{
  "data": {
    "totalResults": 45,
    "results": [
      {
        "id": "17:23",
        "type": "quran",
        "score": 0.963,
        "text": "وَقَضَىٰ رَبُّكَ أَلَّا تَعْبُدُوٓا۟ إِلَّآ إِيَّاهُ وَبِٱلْوَٰلِدَيْنِ إِحْسَـٰنًا ۚ ...",
        "translation": "And your Lord has decreed that you not worship except Him, and to parents, good treatment...",
        "surahNumber": 17,
        "ayahNumber": 23,
        "surahName": "Al-Isra"
      }
    ]
  },
  "meta": {
    "version": "2.0"
  }
}
```

---

## Hadith Search with Scholarly Metadata

When searching Sunnah collections:

```bash
curl -X GET "https://api.kalimat.dev/api/v2/search?query=intentions&contentType=sunnah&hadithSourceBook=bukhari&hadithGrade=sahih&getText=true&getMetadata=true" \
     -H "X-Api-Key: YOUR_KEY"
```

Response snippet:
```json
{
  "data": {
    "results": [
      {
        "id": "bukhari:1",
        "type": "sunnah",
        "score": 0.985,
        "sourceBook": "bukhari",
        "hadithNumber": 1,
        "chapterNumber": 1,
        "grade": "sahih",
        "matnAr": "إنما الأعمال بالنيات وإنما لكل امرئ ما نوى...",
        "translation": "Actions are but by intention and every man shall have but that which he intended...",
        "isnadAr": "حدثنا الحميدي عبد الله بن الزبير قال حدثنا سفيان..."
      }
    ]
  }
}
```

---

## Integration Code Examples

### TypeScript (Search Component Integration)
```typescript
interface SearchResult {
  id: string;
  type: 'quran' | 'sunnah' | 'azkar';
  score: number;
  text?: string;
  translation?: string;
}

export async function semanticIslamicSearch(
  query: string,
  contentType: 'quran' | 'sunnah' | 'azkar' = 'quran',
  apiKey: string
): Promise<SearchResult[]> {
  const params = new URLSearchParams({
    query,
    contentType,
    getText: 'true',
    numResults: '10',
    userLang: 'en',
  });

  const response = await fetch(`https://api.kalimat.dev/api/v2/search?${params.toString()}`, {
    headers: {
      'X-Api-Key': apiKey,
    },
  });

  if (!response.ok) {
    throw new Error(`Kalimat Search error: ${response.statusText}`);
  }

  const payload = await response.json();
  return payload.data.results;
}
```

### Python
```python
import os
import requests

def search_hadith(query_text: str):
    api_key = os.getenv("KALIMAT_API_KEY", "YOUR_KEY_HERE")
    url = "https://api.kalimat.dev/api/v2/search"
    headers = {"X-Api-Key": api_key}
    params = {
        "query": query_text,
        "contentType": "sunnah",
        "hadithSourceBook": "bukhari,muslim",
        "hadithGrade": "sahih",
        "getText": "true",
        "getMetadata": "true",
        "userLang": "en",
        "numResults": 5
    }

    res = requests.get(url, headers=headers, params=params, timeout=10)
    res.raise_for_status()
    results = res.json()["data"]["results"]
    for r in results:
        print(f"[{r['sourceBook'].title()} #{r['hadithNumber']} - {r['grade'].upper()}]")
        print(f"Translation: {r['translation']}\n")

search_hadith("honesty in trade")
```
