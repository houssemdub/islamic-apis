# Islamic Network — Ecosystem Overview & Architecture Guide

**Islamic Network** (`https://islamic.network`) is the premier open-source digital infrastructure provider for Islamic software applications. It powers thousands of mobile apps, websites, and community tools, handling billions of requests and delivering tens of terabytes of media every month.

---

## Ecosystem At A Glance

```
                      ┌─────────────────────────────────────────┐
                      │             ISLAMIC NETWORK             │
                      │          https://islamic.network        │
                      └────────────────────┬────────────────────┘
                                           │
         ┌───────────────────┬─────────────┴───────┬───────────────────┐
         │                   │                     │                   │
         ▼                   ▼                     ▼                   ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────────┐ ┌─────────────────┐
│     AlAdhan     │ │  AlQuran Cloud  │ │  Islamic Net CDN    │ │   Sermons API   │
│  api.aladhan.com│ │api.alquran.cloud│ │ cdn.islamic.network │ │   Khutbah Data  │
│  Prayer & Qibla │ │  Text, Surahs   │ │ MP3 Audio Streams   │ │ Religious Feeds │
└─────────────────┘ └─────────────────┘ └─────────────────────┘ └─────────────────┘
```

---

## Architectural Principles

1. **API-First & Free to Use:** Every service is built API-first and released freely to the global developer community.
2. **Zero Authentication Barrier:** No API keys, tokens, or sign-up walls are required. You can test and deploy apps immediately.
3. **Open Source & Auditable:** All codebases, calculation engines, and text assets are openly hosted on GitHub under permissive licenses.
4. **Community Caching Responsibility:** Because Islamic Network services are run as a free public utility, developers are expected to implement HTTP caching (e.g. caching prayer times daily or monthly, caching Quran text indefinitely in local databases).

---

## Core Services Breakdown

### 1. AlAdhan API (`https://api.aladhan.com/v1`)
The world's most widely used Islamic prayer times and Hijri calendar API.
* **Capabilities:**
  * Coordinates, city, and address lookups.
  * Over 15 standard calculation methods (ISNA, MWL, Umm Al-Qura, Egyptian, etc.).
  * High-latitude adjustments (Angle-based, Midnight, One-Seventh).
  * Gregorian to Hijri and Hijri to Gregorian date conversions.
  * Qibla angle from any coordinates.
  * 99 Names of Allah with meanings.
* *Detailed specification:* See [`aladhan.json`](./aladhan.json) and [`aladhan-doc.md`](./aladhan-doc.md).

---

### 2. AlQuran Cloud API (`https://api.alquran.cloud/v1`)
High-performance REST API for the complete Holy Quran.
* **Capabilities:**
  * Full Arabic text in Uthmani and simple scripts.
  * 80+ translations in dozens of languages (English, Urdu, French, Indonesian, Spanish, Turkish, etc.).
  * Verse-by-verse and chapter-by-chapter lookups.
  * Structural markers: Surahs, Ayahs, Juzs, Hizbs, Manzils, Rukus, Sajdas.
  * Full-text search across Quranic editions.
* *Detailed specification:* See [`alquran-cloud.json`](./alquran-cloud.json) and [`alquran-cloud-doc.md`](./alquran-cloud-doc.md).

---

### 3. Islamic Network Audio CDN (`https://cdn.islamic.network`)
Global CDN specifically optimized for Quranic audio streaming and downloadable MP3 assets.
* **Audio URL Pattern:**
  ```http
  https://cdn.islamic.network/quran/audio/{bitrate}/{edition}/{ayah_number}.mp3
  ```
* **Bitrates:** `64`, `128`, `192` kbps.
* **Ayah Number:** Global ayah index (1 to 6236).
* **Popular Reciters:**
  * `ar.alafasy` — Mishary Rashid Alafasy
  * `ar.abdulbasitmurattal` — Abdul Basit (Murattal)
  * `ar.abdulsamad` — Abdul Basit (Mujawwad)
  * `ar.hudhaify` — Ali Al-Hudhaify
  * `ar.husary` — Mahmoud Khalil Al-Husary
  * `ar.minshawi` — Mohamed Siddiq El-Minshawi
  * `ar.saoodshuraym` — Sa'ud Ash-Shuraym
  * `ar.abdurrahmaansudais` — Abdur-Rahman As-Sudais

#### Example Streaming Link:
```html
<audio controls src="https://cdn.islamic.network/quran/audio/128/ar.alafasy/1.mp3">
  Al-Fatiha Verse 1
</audio>
```

---

### 4. Sermons (Khutbas) API (`https://api.sermons.islamic.network/v1`)
Aggregates official Friday sermons (Khutbahs) delivered by recognized authorities across the Islamic world.
* **Endpoints:**
  * `GET /sources` — List sermon providers/ministries.
  * `GET /languages` — List supported languages.
  * `GET /sermons` — List recent sermons with dates, topics, and PDF/text transcripts.

---

## Best Practices for Integrating Islamic Network

### 1. Client-Side & Edge Caching
* **Prayer Times:** Prayer times change only once per day. Cache queries for a full 24 hours. Even better: fetch the full monthly calendar `/calendarByCity` on the 1st of each month and store it in SQLite / IndexedDB / AsyncStorage.
* **Quran Text:** The Quran does not change. Cache Surah and Ayah JSON payloads permanently in your local database.
* **Audio:** Cache downloaded audio files in the application's cache directory to reduce CDN load and allow offline listening.

### 2. Error Handling & Fallbacks
When network connectivity is weak or CDN nodes experience temporary latency:
```typescript
async function fetchWithRetry(url: string, retries = 3) {
  for (let i = 0; i < retries; i++) {
    try {
      const res = await fetch(url);
      if (res.ok) return await res.json();
    } catch (err) {
      if (i === retries - 1) throw err;
      await new Promise(r => setTimeout(r, 1000 * Math.pow(2, i)));
    }
  }
}
```

### 3. Operational Status
Check the live availability of all services on [status.islamic.network](https://status.islamic.network).
