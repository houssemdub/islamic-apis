# Fawaz Ahmed Hadith REST API Integration Guide

Comprehensive documentation for developers integrating canonical Prophetic Traditions (**Hadith**) using the CDN-backed JSON API created by **Fawaz Ahmed**.

---

## 1. Overview & Architecture

The Fawaz Ahmed Hadith API is served statically through the high-performance **jsDelivr CDN** directly from GitHub. It offers zero rate limits, instantaneous response times, and comprehensive coverage of primary Hadith books.

- **Primary Base URL:** `https://cdn.jsdelivr.net/gh/fawazahmed0/hadith-api@1`
- **Fallback Base URL:** `https://raw.githubusercontent.com/fawazahmed0/hadith-api/1`
- **Formats Available:** Standard `.json` and minified `.min.json` for all endpoints.
- **Authentication:** None (Completely Free & Keyless).
- **Rate Limit:** Unbounded (Subject to jsDelivr CDN limits).

---

## 2. Canonical Hadith Books Included

The API indexes classical collections including:
- **Sahih al-Bukhari** (`ara-bukhari`, `eng-bukhari`, `urd-bukhari`, etc.)
- **Sahih Muslim** (`ara-muslim`, `eng-muslim`, `urd-muslim`)
- **Sunan an-Nasa'i** (`ara-nasai`, `eng-nasai`)
- **Sunan Abi Dawud** (`ara-abudawud`, `eng-abudawud`)
- **Jami` at-Tirmidhi** (`ara-tirmidhi`, `eng-tirmidhi`)
- **Sunan Ibn Majah** (`ara-ibnmajah`, `eng-ibnmajah`)
- **Muwatta Malik** (`ara-malik`, `eng-malik`)
- **Musnad Ahmad**, **Riyad as-Salihin**, **Mishkat al-Masabih**, and **40 Hadith Nawawi**.

---

## 3. Endpoints & Usage

### 3.1 Metadata & Grade Information (`GET /info.json`)
Provides metadata on books, scholarly grades (Sahih, Hasan, Da'if, Maudu'), chapter structures, and available languages.

```bash
curl "https://cdn.jsdelivr.net/gh/fawazahmed0/hadith-api@1/info.json"
```

### 3.2 List All Editions (`GET /editions.json`)
Lists every available edition slug by language, author, and book code.

```bash
curl "https://cdn.jsdelivr.net/gh/fawazahmed0/hadith-api@1/editions.json"
```

### 3.3 Query Single Hadith by Number
Retrieve a specific hadith in English or Arabic directly:

```bash
# English Sahih Bukhari Hadith #1 (Actions by Intentions)
curl "https://cdn.jsdelivr.net/gh/fawazahmed0/hadith-api@1/editions/eng-bukhari/1.json"

# Arabic Sahih Bukhari Hadith #1
curl "https://cdn.jsdelivr.net/gh/fawazahmed0/hadith-api@1/editions/ara-bukhari/1.json"
```

**Sample Output Schema:**
```json
{
  "hadiths": [
    {
      "hadithnumber": 1,
      "arabicnumber": 1,
      "text": "Narrated 'Umar bin Al-Khattab: I heard Allah's Messenger saying, 'The reward of deeds depends upon the intentions and every person will get the reward according to what he has intended...'",
      "grades": [
        {
          "name": "Al-Albani",
          "grade": "Sahih"
        }
      ],
      "reference": {
        "book": 1,
        "hadith": 1
      }
    }
  ]
}
```

### 3.4 Query a Chapter / Section
```bash
# Fetch Section 1 of Sahih Bukhari (Revelation)
curl "https://cdn.jsdelivr.net/gh/fawazahmed0/hadith-api@1/editions/eng-bukhari/sections/1.json"
```

---

## 4. Resilient TypeScript Client with Fallback

```typescript
const PRIMARY_CDN = "https://cdn.jsdelivr.net/gh/fawazahmed0/hadith-api@1";
const FALLBACK_CDN = "https://raw.githubusercontent.com/fawazahmed0/hadith-api/1";

export async function fetchHadith(edition: string, hadithNo: number): Promise<any> {
  const path = `/editions/${edition}/${hadithNo}.min.json`;
  try {
    const res = await fetch(`${PRIMARY_CDN}${path}`);
    if (!res.ok) throw new Error(`Primary CDN error: ${res.status}`);
    return await res.json();
  } catch (err) {
    console.warn("Falling back to raw GitHub CDN", err);
    const fallbackRes = await fetch(`${FALLBACK_CDN}${path}`);
    if (!fallbackRes.ok) throw new Error(`Fallback error: ${fallbackRes.status}`);
    return await fallbackRes.json();
  }
}
```
