<div align="center">

<img src="assets/logo.png" alt="Islamic APIs Hub Logo" width="220" />

# 🕌 Islamic APIs Hub & Registry

**The definitive, curated registry and developer hub of authentic, high-performance Islamic APIs, libraries, and datasets.**

[![License: MIT](https://img.shields.io/badge/License-MIT-emerald.svg)](LICENSE)
[![APIs Indexed](https://img.shields.io/badge/APIs_Indexed-15_Services-0ea5e9.svg)](index.json)
[![Schema Version](https://img.shields.io/badge/Schema-JSON_Schema_2020--12-blueviolet.svg)](index.json)
[![PRs Welcome](https://img.shields.io/badge/PRs-Welcome-brightgreen.svg)](CONTRIBUTING.md)

[**Explore Documentation (DOCS.md)**](DOCS.md) • [**JSON Registry (index.json)**](index.json) • [**API Specifications**](apis/) • [**Integration Guides**](docs/apis/)

</div>

---

## 🌟 Overview

Building reliable, accurate, and high-performance Islamic software requires dependable APIs for prayer calculations, astronomical moon phases, authentic Quranic manuscripts, multi-lingual Hadith collections, and scholarly commentaries (Tafsirs).

This repository serves as a centralized, standardized developer resource offering:
- **15+ Verified Islamic APIs & Libraries** categorized across 7 core functional domains.
- **Machine-readable JSON Specifications** (`apis/`) with full route parameter mappings.
- **In-depth Integration Guides & Code Samples** (`docs/apis/`) for Node.js, Python, Flutter/Dart, and cURL.
- **Offline & Fallback Strategies** for resilient mobile and web development.

---

## 📂 Repository Directory Structure

```text
islamic-apis/
├── apis/                          # Standardized JSON API Specifications
│   ├── all-in-one/                # Multi-service suites (UmmahAPI, Islamic Network)
│   ├── prayer-times/              # Astronomical calculation engines & geocoding
│   ├── quran/                     # Quranic text, word-by-word linguistics & AI search
│   ├── quran-audio/               # 250+ Reciters, 24/7 internet radio & live feeds
│   ├── hadith/                    # Canonical Hadith collections & authenticity gradings
│   ├── tafsir/                    # Classical and contemporary Quranic commentaries
│   └── duas-adhkar/               # Daily remembrances & Fortress of the Muslim
├── docs/                          # Comprehensive Developer Guides
│   └── apis/                      # Step-by-step markdown documentation per API
├── index.json                     # Machine-readable registry catalog
├── DOCS.md                        # Master technical documentation & architecture guide
└── README.md                      # Project landing page & quick start
```

---

## 📊 Curated APIs Directory

### 1. 🌐 All-in-One Multi-Service Platforms
| API | Description | Auth | Links |
|---|---|---|---|
| **[UmmahAPI](apis/all-in-one/ummahapi.json)** | 23 calculation methods, Quran, Hadith, Qibla, Duas, MCP Server | Free / Optional Key | [Docs](docs/apis/ummahapi-doc.md) • [Spec](apis/all-in-one/ummahapi.json) |
| **[Islamic Network](apis/all-in-one/islamic-network.json)** | Ecosystem backing AlAdhan, AlQuran Cloud, Audio CDN & Khutbahs | Free / Keyless | [Docs](docs/apis/islamic-network-doc.md) • [Spec](apis/all-in-one/islamic-network.json) |

### 2. ⏰ Prayer Times, Qibla & Calendars
| API | Description | Auth | Links |
|---|---|---|---|
| **[AlAdhan API](apis/prayer-times/aladhan.json)** | Astronomical calculations, 20+ methods, Hijri calendars, Qibla angle | Free / Keyless | [Docs](docs/apis/aladhan-doc.md) • [Spec](apis/prayer-times/aladhan.json) |
| **[IslamCave](apis/prayer-times/islamcave.json)** | Single GET endpoint for global 5 daily prayer times & Qibla bearing | Keyless (Attribution) | [Docs](docs/apis/islamcave-doc.md) • [Spec](apis/prayer-times/islamcave.json) |
| **[Kalimat Prayer Times](apis/prayer-times/kalimat-prayer-times.json)** | Enterprise nearest-city geocoding with verified municipality schedules | API Key Required | [Docs](docs/apis/kalimat-prayer-times-doc.md) • [Spec](apis/prayer-times/kalimat-prayer-times.json) |
| **[PrayerZone](apis/prayer-times/prayer-zone.json)** | Flutter/Dart offline astronomical engine with Algerian Ministry method | Open Source Lib | [Docs](docs/apis/prayer-zone-doc.md) • [Spec](apis/prayer-times/prayer-zone.json) |

### 3. 📖 Quran Text, Linguistics & AI Search
| API | Description | Auth | Links |
|---|---|---|---|
| **[AlQuran Cloud](apis/quran/alquran-cloud.json)** | 114 Surahs, 80+ translations, multi-edition batch requests | Free / Keyless | [Docs](docs/apis/alquran-cloud-doc.md) • [Spec](apis/quran/alquran-cloud.json) |
| **[Quran.com (v4)](apis/quran/quran-com.json)** | Word-by-word linguistics, audio segments, Tajweed, translations | Free Public | [Docs](docs/apis/quran-com-doc.md) • [Spec](apis/quran/quran-com.json) |
| **[Fawaz Ahmed Quran](apis/quran/fawaz-quran.json)** | 440+ editions across 90+ languages hosted on high-speed jsDelivr CDN | Free / Keyless CDN | [Docs](docs/apis/fawaz-quran-doc.md) • [Spec](apis/quran/fawaz-quran.json) |
| **[Kalimat AI Search](apis/quran/kalimat-search.json)** | Semantic vector search enabling natural-language conceptual Quran queries | API Key Required | [Docs](docs/apis/kalimat-search-doc.md) • [Spec](apis/quran/kalimat-search.json) |

### 4. 🎧 Quran Audio Recitations & Live Streams
| API | Description | Auth | Links |
|---|---|---|---|
| **[MP3Quran (v3)](apis/quran-audio/mp3quran.json)** | 250+ Reciters, 150+ live 24/7 radio stations, Makkah/Madinah Live TV | Free / Keyless CDN | [Docs](docs/apis/mp3quran-doc.md) • [Spec](apis/quran-audio/mp3quran.json) |

### 5. 📜 Hadith & Prophetic Traditions
| API | Description | Auth | Links |
|---|---|---|---|
| **[Fawaz Ahmed Hadith](apis/hadith/fawaz-hadith.json)** | Kutub al-Sittah + Malik + Ahmad, authenticity gradings, multilingual | Free / Keyless CDN | [Docs](docs/apis/fawaz-hadith-doc.md) • [Spec](apis/hadith/fawaz-hadith.json) |
| **[HadithAPI.com](apis/hadith/hadithapi.json)** | Searchable canonical books in Arabic, English & Urdu with grade filters | Free API Key | [Docs](docs/apis/hadithapi-doc.md) • [Spec](apis/hadith/hadithapi.json) |

### 6. 🔍 Tafsir & Quran Commentary
| API | Description | Auth | Links |
|---|---|---|---|
| **[Quran Tafseer](apis/tafsir/quran-tafseer.json)** | Ibn Kathir, Al-Muyassar, Al-Sa'di, Al-Jalalayn, Al-Qurtubi by verse/range | Free / Keyless | [Docs](docs/apis/quran-tafseer-doc.md) • [Spec](apis/tafsir/quran-tafseer.json) |

### 7. 🤲 Duas & Daily Remembrances (Adhkar)
| API | Description | Auth | Links |
|---|---|---|---|
| **[Hisn al-Muslim](apis/duas-adhkar/hisn-almuslim.json)** | Complete Fortress of the Muslim (132+ categories) with audio & counters | Free Open Access | [Docs](docs/apis/hisn-almuslim-doc.md) • [Spec](apis/duas-adhkar/hisn-almuslim.json) |

---

## 🚀 Quick Start Examples

### 1. Fetch Today's Prayer Times (cURL)
```bash
# London, UK (Method 3: Muslim World League)
curl "https://api.aladhan.com/v1/timingsByCity?city=London&country=United+Kingdom&method=3"
```

### 2. Stream Audio Recitation (JavaScript)
```javascript
// Stream Surah Al-Kahf (018) recited by Mishary Rashid Alafasy
const audioUrl = "https://server8.mp3quran.net/afs/018.mp3";
const player = new Audio(audioUrl);
player.play();
```

### 3. Read an Ayah with English Translation (Python)
```python
import requests

res = requests.get("https://cdn.jsdelivr.net/gh/fawazahmed0/quran-api@1/editions/eng-abdelhaleem/2/255.json")
ayah_data = res.json()
print("Ayat al-Kursi:", ayah_data["text"])
```

---

## 🛠️ Machine-Readable Registry

You can consume this entire catalog programmatically using `index.json`:

```typescript
import registry from './index.json';

// Get all prayer time providers
const prayerApis = registry.categories.find(c => c.id === 'prayer-times').apis;
console.log(prayerApis.map(a => a.name));
```

---

## 🤝 Contributing

Contributions are warmly welcomed! If you operate or know of an authentic Islamic API, open-source library, or verified dataset:
1. Fork this repository.
2. Add your JSON specification into the matching category in `apis/`.
3. Add a technical integration markdown document in `docs/apis/`.
4. Update `index.json` and `README.md`.
5. Submit a Pull Request.

---

## 📄 License

This repository and its documentation are open-source and released under the [MIT License](LICENSE).
