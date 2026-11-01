# 📖 Islamic APIs Registry & Developer Guide (DOCS.md)

Welcome to the **Islamic APIs Registry & Developer Guide**. This document provides an exhaustive technical reference covering architecture, integration models, authentication strategies, rate-limiting best practices, and API specifications for building modern Islamic digital applications.

---

## 📑 Table of Contents
1. [Architecture & Design Principles](#1-architecture--design-principles)
2. [Category Breakdown & Capability Matrix](#2-category-breakdown--capability-matrix)
3. [Authentication Strategies](#3-authentication-strategies)
4. [Caching, Performance & Offline-First Design](#4-caching-performance--offline-first-design)
5. [Complete API Specifications](#5-complete-api-specifications)
   - [All-in-One & Multi-Service Platforms](#51-all-in-one--multi-service-platforms)
   - [Prayer Times, Qibla & Calendar APIs](#52-prayer-times-qibla--calendar-apis)
   - [Quran Text & Search APIs](#53-quran-text--search-apis)
   - [Quran Audio Recitation CDNs](#54-quran-audio-recitation-cdns)
   - [Hadith & Prophetic Traditions APIs](#55-hadith--prophetic-traditions-apis)
   - [Tafsir & Exegesis APIs](#56-tafsir--exegesis-apis)
   - [Duas & Daily Remembrances (Adhkar)](#57-duas--daily-remembrances-adhkar)
6. [SDKs, Libraries & Framework Integrations](#6-sdks-libraries--framework-integrations)
7. [Contributing & Adding New APIs](#7-contributing--adding-new-apis)

---

## 1. Architecture & Design Principles

Building Islamic applications (prayer reminders, digital mushafs, audio players, hadith research tools) requires high reliability, absolute textual accuracy (harakat/diacritics), and low latency across global networks.

### Standardized JSON Registry Format
Every API in this registry is mapped by a machine-readable schema located in `apis/` containing:
- **`name` & `category`**: Human-readable metadata and functional domain.
- **`baseUrl` & `fallbackBaseUrl`**: Primary endpoints and CDN fallbacks.
- **`authentication`**: Keyless vs API key requirements.
- **`endpoints`**: Structured breakdown of routes, methods, path parameters, and query parameters.

### Recommended Architectural Patterns
1. **Hybrid Client/Server Calculation for Prayer Times:**
   - Use remote geocoded APIs (e.g. AlAdhan, Kalimat) when network connectivity is available.
   - Cache coordinates and run local astronomical algorithms (e.g. `prayer_zone` or Adhan Dart/JS) when offline.
2. **Audio Streaming with Multi-Qari Fallbacks:**
   - Utilize CDN endpoints (MP3Quran, Islamic Network Audio CDN) using standardized zero-padded 3-digit Surah identifiers (`001.mp3` - `114.mp3`).
3. **Immutability of Quranic Text:**
   - Never mutate or strip diacritical marks (Tashkeel) during ingestion unless specifically indexing for normalized search.

---

## 2. Category Breakdown & Capability Matrix

| ID | Category | Primary Focus | Best For |
|---|---|---|---|
| `all-in-one` | All-in-One Multi-Service | Complete Islamic suites (Prayer + Quran + Hadith + Duas) | Rapid MVP development & unified backends |
| `prayer-times` | Prayer Times & Calendars | High-accuracy astronomical times, Qibla compass, Hijri conversion | Mobile Adhan alerts, smart watches, mosque displays |
| `quran` | Quran Text & Search | Arabic Uthmani text, 90+ language translations, semantic vector search | Digital Mushafs, study apps, semantic AI search |
| `quran-audio` | Quran Audio & Radio | 250+ Reciters, 24/7 Quran internet radio stations, Live TV | Background audio players, IoT smart speakers |
| `hadith` | Hadith Collections | Kutub al-Sittah, authenticity gradings (Sahih/Da'if), multilingual | Hadith search engines, daily Sunnah reminders |
| `tafsir` | Tafsir & Exegesis | Classical & modern scholarly commentaries (Ibn Kathir, Al-Muyassar) | In-depth Quran study platforms |
| `duas-adhkar` | Duas & Daily Adhkar | Hisn al-Muslim (Fortress of the Muslim), counters, vocalized Arabic | Morning/evening Azkar trackers, daily supplications |

---

## 3. Authentication Strategies

| Provider | Authentication Type | Credentials Setup |
|---|---|---|
| **AlAdhan** | Free / Keyless | None required. Direct HTTP GET. |
| **AlQuran Cloud** | Free / Keyless | None required. Direct HTTP GET. |
| **MP3Quran (v3)** | Free / Keyless | None required. Global CDN access. |
| **Fawaz Ahmed Hadith & Quran** | Free / Keyless CDN | None required. Served via jsDelivr CDN edge. |
| **Quran Tafseer API** | Free / Keyless | None required. Direct HTTP GET. |
| **Hisn al-Muslim Open API** | Free / Keyless | None required. Direct GitHub / CDN JSON dataset. |
| **Quran.com (v4)** | Free Public | Public endpoints without auth; optional keys for high throughput. |
| **UmmahAPI** | Free / Optional Key | Keyless for default tiers; register at `ummahapi.com` for elevated quotas. |
| **HadithAPI.com** | API Key Required | Instant free registration at `hadithapi.com` (`?apiKey=...`). |
| **Kalimat (Prayer & Search)** | API Key Required | Register at `kalimat.dev` for semantic AI vector search & prayer endpoints. |

---

## 4. Caching, Performance & Offline-First Design

### A. HTTP Caching Headers
Quranic text and canonical Hadiths are immutable. Your HTTP clients or reverse proxies should enforce aggressive caching:
```http
Cache-Control: public, max-age=31536000, immutable
```

### B. Fallback Strategy for CDN-Hosted APIs
When consuming static CDN-backed APIs (e.g., Fawaz Ahmed Hadith or Quran), implement automatic failover:
```typescript
async function fetchWithFallback(path: string) {
  const cdnUrl = `https://cdn.jsdelivr.net/gh/fawazahmed0/hadith-api@1${path}`;
  const rawUrl = `https://raw.githubusercontent.com/fawazahmed0/hadith-api/1${path}`;
  
  try {
    const res = await fetch(cdnUrl);
    if (!res.ok) throw new Error("CDN status " + res.status);
    return await res.json();
  } catch (e) {
    const fallbackRes = await fetch(rawUrl);
    return await fallbackRes.json();
  }
}
```

---

## 5. Complete API Specifications

### 5.1 All-in-One & Multi-Service Platforms

#### 1. UmmahAPI
- **Base URL:** `https://ummahapi.com`
- **Documentation:** [`docs/apis/ummahapi-doc.md`](docs/apis/ummahapi-doc.md) | **Spec:** [`apis/all-in-one/ummahapi.json`](apis/all-in-one/ummahapi.json)
- **Features:** 23 calculation methods, Quran, Hadith, Qibla compass, Duas, Asma ul-Husna, Tafsir, Zakat calculator, Model Context Protocol (MCP) server.

#### 2. Islamic Network
- **Base URL:** `https://islamic.network`
- **Documentation:** [`docs/apis/islamic-network-doc.md`](docs/apis/islamic-network-doc.md) | **Spec:** [`apis/all-in-one/islamic-network.json`](apis/all-in-one/islamic-network.json)
- **Features:** Umbrella ecosystem powering AlAdhan and AlQuran Cloud, global audio CDN, Friday Sermons (Khutbahs) API, official TypeScript SDK.

---

### 5.2 Prayer Times, Qibla & Calendar APIs

#### 1. AlAdhan Prayer Times & Calendar API
- **Base URL:** `https://api.aladhan.com/v1`
- **Documentation:** [`docs/apis/aladhan-doc.md`](docs/apis/aladhan-doc.md) | **Spec:** [`apis/prayer-times/aladhan.json`](apis/prayer-times/aladhan.json)
- **Key Endpoints:**
  - `GET /timings/{timestamp}?latitude={lat}&longitude={lng}&method={method}`
  - `GET /timingsByCity?city={city}&country={country}`
  - `GET /calendar/{year}/{month}`
  - `GET /qibla/{latitude}/{longitude}`
  - `GET /gToH` (Gregorian to Hijri calendar conversion)

#### 2. IslamCave Prayer Times API
- **Base URL:** `https://islamcave.com/api`
- **Documentation:** [`docs/apis/islamcave-doc.md`](docs/apis/islamcave-doc.md) | **Spec:** [`apis/prayer-times/islamcave.json`](apis/prayer-times/islamcave.json)
- **Highlights:** Ultra-lightweight single GET endpoint returning formatted 12-hour prayer times, Fajr angle, and Qibla bearing.

#### 3. Kalimat Prayer Times API
- **Base URL:** `https://api.kalimat.dev/api/v2`
- **Documentation:** [`docs/apis/kalimat-prayer-times-doc.md`](docs/apis/kalimat-prayer-times-doc.md) | **Spec:** [`apis/prayer-times/kalimat-prayer-times.json`](apis/prayer-times/kalimat-prayer-times.json)
- **Highlights:** Enterprise nearest-city geocoding, verified municipal schedules, high-availability SLA.

#### 4. PrayerZone (Dart / Flutter Library)
- **Base URL:** `https://pub.dev/packages/prayer_zone`
- **Documentation:** [`docs/apis/prayer-zone-doc.md`](docs/apis/prayer-zone-doc.md) | **Spec:** [`apis/prayer-times/prayer-zone.json`](apis/prayer-times/prayer-zone.json)
- **Highlights:** Completely offline calculation engine, 23 astronomical calculation methods, official Algerian Ministry parameters (18° Fajr / 17° Isha), 182 countries geocoded.

---

### 5.3 Quran Text & Search APIs

#### 1. AlQuran Cloud API
- **Base URL:** `https://api.alquran.cloud/v1`
- **Documentation:** [`docs/apis/alquran-cloud-doc.md`](docs/apis/alquran-cloud-doc.md) | **Spec:** [`apis/quran/alquran-cloud.json`](apis/quran/alquran-cloud.json)
- **Key Endpoints:**
  - `GET /surah/{number}/{edition}`: Multiple editions queryable via comma-separated slugs (`quran-uthmani,en.asad,ar.alafasy`).
  - `GET /ayah/{reference}/{edition}`: Lookup by global Ayah number or `surah:ayah`.
  - `GET /juz/{number}/{edition}`: Ingest full Juz in a single call.

#### 2. Quran.com API (v4)
- **Base URL:** `https://api.quran.com/api/v4`
- **Documentation:** [`docs/apis/quran-com-doc.md`](docs/apis/quran-com-doc.md) | **Spec:** [`apis/quran/quran-com.json`](apis/quran/quran-com.json)
- **Key Endpoints:**
  - `GET /verses/by_chapter/{chapter_number}`: Word-by-word linguistics, audio segments, Tajweed annotations.
  - `GET /quran/translations/{translation_id}`: Over 100 translations.
  - `GET /search?q={query}`: Multi-language full-text search.

#### 3. Fawaz Ahmed Quran API
- **Base URL:** `https://cdn.jsdelivr.net/gh/fawazahmed0/quran-api@1`
- **Documentation:** [`docs/apis/fawaz-quran-doc.md`](docs/apis/fawaz-quran-doc.md) | **Spec:** [`apis/quran/fawaz-quran.json`](apis/quran/fawaz-quran.json)
- **Highlights:** 440+ editions, 90+ languages, static CDN edge hosting with zero throttling.

#### 4. Kalimat Quran AI Vector Search API
- **Base URL:** `https://api.kalimat.dev/api/v2`
- **Documentation:** [`docs/apis/kalimat-search-doc.md`](docs/apis/kalimat-search-doc.md) | **Spec:** [`apis/quran/kalimat-search.json`](apis/quran/kalimat-search.json)
- **Highlights:** AI semantic vector search allowing natural-language conceptual queries (e.g., *"verses about patience in hardship"* or *"economic ethics"*).

---

### 5.4 Quran Audio Recitation CDNs

#### 1. MP3Quran.net API (v3)
- **Base URL:** `https://mp3quran.net/api/v3`
- **Documentation:** [`docs/apis/mp3quran-doc.md`](docs/apis/mp3quran-doc.md) | **Spec:** [`apis/quran-audio/mp3quran.json`](apis/quran-audio/mp3quran.json)
- **Highlights:**
  - 250+ world-renowned Qurra (Mishary Alafasy, Abdulbasit, Al-Sudais, Al-Shuraim, Al-Minshawi, etc.).
  - 150+ live 24/7 internet radio stations.
  - Official Makkah & Madinah live sanctuary video streams.
- **Audio CDN Construction:**
  `{moshaf.server}{surah_number_padded_to_3_digits}.mp3` (e.g. `https://server8.mp3quran.net/afs/001.mp3`).

---

### 5.5 Hadith & Prophetic Traditions APIs

#### 1. Fawaz Ahmed Hadith API
- **Base URL:** `https://cdn.jsdelivr.net/gh/fawazahmed0/hadith-api@1`
- **Documentation:** [`docs/apis/fawaz-hadith-doc.md`](docs/apis/fawaz-hadith-doc.md) | **Spec:** [`apis/hadith/fawaz-hadith.json`](apis/hadith/fawaz-hadith.json)
- **Highlights:** Kutub al-Sittah (Bukhari, Muslim, Abu Dawud, Tirmidhi, Nasa'i, Ibn Majah) plus Muwatta Malik and Musnad Ahmad. Includes scholarly authenticity gradings (`Sahih`, `Hasan`, `Da'if`).

#### 2. HadithAPI.com
- **Base URL:** `https://hadithapi.com/api`
- **Documentation:** [`docs/apis/hadithapi-doc.md`](docs/apis/hadithapi-doc.md) | **Spec:** [`apis/hadith/hadithapi.json`](apis/hadith/hadithapi.json)
- **Highlights:** Authenticated REST API with full-text search across Arabic, English, and Urdu, chapter listings, and authenticity status filtering.

---

### 5.6 Tafsir & Exegesis APIs

#### 1. Quran Tafseer API
- **Base URL:** `http://api.quran-tafseer.com`
- **Documentation:** [`docs/apis/quran-tafseer-doc.md`](docs/apis/quran-tafseer-doc.md) | **Spec:** [`apis/tafsir/quran-tafseer.json`](apis/tafsir/quran-tafseer.json)
- **Key Endpoints:**
  - `GET /tafseer`: List all commentaries (Al-Muyassar, Ibn Kathir, Al-Jalalayn, Al-Sa'di, Al-Qurtubi, Al-Tabari).
  - `GET /tafseer/{tafseer_id}/{sura}/{ayah}`: Single verse interpretation.
  - `GET /tafseer/{tafseer_id}/{sura}/{from_ayah}/{to_ayah}`: Multi-verse contiguous commentary range.

---

### 5.7 Duas & Daily Remembrances (Adhkar)

#### 1. Hisn al-Muslim & Azkar API
- **Base URL:** `https://raw.githubusercontent.com/rn0x/hisn_almuslim_json/main`
- **CDN Base URL:** `https://cdn.jsdelivr.net/gh/rn0x/hisn_almuslim_json@main`
- **Documentation:** [`docs/apis/hisn-almuslim-doc.md`](docs/apis/duas-adhkar/hisn-almuslim-doc.md) | **Spec:** [`apis/duas-adhkar/hisn-almuslim.json`](apis/duas-adhkar/hisn-almuslim.json)
- **Highlights:** Complete *Fortress of the Muslim* collection (132+ categories). Includes Arabic vocalization, repetition targets, reference citations, and audio URLs.

---

## 6. SDKs, Libraries & Framework Integrations

### Python Integration Example
```python
import requests

def get_prayer_timings(lat: float, lng: float):
    url = f"https://api.aladhan.com/v1/timings?latitude={lat}&longitude={lng}&method=3"
    response = requests.get(url, timeout=5)
    response.raise_for_status()
    data = response.json()
    return data["data"]["timings"]

def get_surah_english(surah_no: int):
    url = f"https://cdn.jsdelivr.net/gh/fawazahmed0/quran-api@1/editions/eng-abdelhaleem/{surah_no}.min.json"
    res = requests.get(url, timeout=5)
    return res.json()["chapter"]
```

### TypeScript / Node.js Integration Example
```typescript
import axios from 'axios';

export async function searchHadith(query: string, apiKey: string) {
  const response = await axios.get('https://hadithapi.com/api/hadiths', {
    params: {
      apiKey,
      hadithEnglish: query,
      paginate: 5
    }
  });
  return response.data;
}
```

---

## 7. Contributing & Adding New APIs

We welcome additions of authentic, high-quality Islamic APIs!

To add an API:
1. Choose or create the relevant category folder inside `apis/<category>/`.
2. Add the JSON specification matching the schema in `apis/all-in-one/ummahapi.json`.
3. Create the comprehensive integration markdown guide in `docs/apis/<api-name>-doc.md`.
4. Register the entry inside root `index.json`.
5. Open a Pull Request!
