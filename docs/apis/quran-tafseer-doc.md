# Quran Tafseer REST API Integration Guide

Comprehensive documentation for developers integrating classical and contemporary Quranic exegesis (**Tafsir**) via `api.quran-tafseer.com`.

---

## 1. Overview

The Quran Tafseer API is an open, community-oriented REST API dedicated solely to Quranic commentaries. It provides structured access to prominent scholarly works without requiring API tokens.

- **Base URL:** `http://api.quran-tafseer.com`
- **Authentication:** None (Open & Keyless)
- **Protocol:** HTTP GET
- **Format:** JSON

---

## 2. Core Endpoints

### 2.1 List All Tafseer Editions (`GET /tafseer`)
Lists all supported Tafsir editions with their numeric `id`, author, language, and book name.

```bash
curl "http://api.quran-tafseer.com/tafseer"
```

**Common Tafsir IDs:**
- `1`: التفسير الميسر (Al-Tafsir Al-Muyassar - Arabic)
- `2`: تفسير الجلالين (Tafsir Al-Jalalayn - Arabic)
- `3`: تفسير السعدي (Tafsir Al-Sa'di - Arabic)
- `4`: تفسير ابن كثير (Tafsir Ibn Kathir - Arabic)
- `6`: تفسير البغوي (Tafsir Al-Baghawi - Arabic)
- `7`: تفسir القرطبي (Tafsir Al-Qurtubi - Arabic)
- `8`: تفسير الطبري (Tafsir Al-Tabari - Arabic)
- `10`: Tafsir Ibn Kathir (English Translation)

---

### 2.2 Get Single Verse Tafseer
Fetch the explanation for a specific verse:

```bash
# Format: /tafseer/{tafseer_id}/{sura_number}/{ayah_number}
# Example: Surah Al-Fatihah, Ayah 1 using Al-Muyassar (id: 1)
curl "http://api.quran-tafseer.com/tafseer/1/1/1"
```

**Response Schema:**
```json
{
  "tafseer_id": 1,
  "tafseer_name": "التفسير الميسر",
  "ayah_url": "/quran/1/1/",
  "ayah_number": 1,
  "text": "سورة الفاتحة سميت هذه السورة بفاتحة الكتاب؛ لأن القرآن افتتح بها..."
}
```

---

### 2.3 Get Verse Range Tafseer
Fetch explanations for a range of consecutive verses:

```bash
# Format: /tafseer/{tafseer_id}/{sura_number}/{ayah_from}/{ayah_to}
# Example: Surah Al-Baqarah (2), Ayahs 1 to 5
curl "http://api.quran-tafseer.com/tafseer/1/2/1/5"
```

---

## 3. JavaScript / Python Usage

### Python with `requests`
```python
import requests

def get_ayah_tafseer(sura: int, ayah: int, tafseer_id: int = 1) -> str:
    url = f"http://api.quran-tafseer.com/tafseer/{tafseer_id}/{sura}/{ayah}"
    res = requests.get(url, timeout=10)
    res.raise_for_status()
    data = res.json()
    return data.get("text", "")
```
