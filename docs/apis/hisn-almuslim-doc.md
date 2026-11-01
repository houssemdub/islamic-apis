# Hisn al-Muslim & Azkar API Integration Guide

Comprehensive documentation for developers integrating daily Islamic supplications (**Duas & Adhkar**) from **Hisn al-Muslim** (*Fortress of the Muslim* by Sheikh Sa'id bin Ali bin Wahf Al-Qahtani).

---

## 1. Overview

Hisn al-Muslim is the most widely distributed collection of authentic daily supplications and remembrances. This open-source API dataset provides structured, indexed JSON endpoints for easy integration into web, iOS, Android, and desktop applications.

- **Base URL:** `https://raw.githubusercontent.com/rn0x/hisn_almuslim_json/main`
- **CDN Base URL:** `https://cdn.jsdelivr.net/gh/rn0x/hisn_almuslim_json@main`
- **Authentication:** None (Public Open Access)
- **Format:** JSON

---

## 2. Core Endpoints

### 2.1 Complete Hisn al-Muslim (Arabic with Vocalization & Counts)
```bash
curl "https://raw.githubusercontent.com/rn0x/hisn_almuslim_json/main/hisn_almuslim.json"
```

**JSON Data Structure:**
```json
[
  {
    "ID": 1,
    "TITLE": "أذكار الاستيقاظ من النوم",
    "AUDIO": "https://server.mp3/001.mp3",
    "TEXT": "الْحَمْدُ لِلَّهِ الَّذِي أَحْيَانَا بَعْدَ مَا أَمَاتَنَا وَإِلَيْهِ النُّشُورُ",
    "REPEAT": 1,
    "ORDER": 1
  }
]
```

---

## 3. Key Categories Included

1. **Morning & Evening Adhkar** (أذكار الصباح والمساء)
2. **Waking Up & Sleeping** (أذكار الاستيقاظ والنوم)
3. **Before & After Salah** (أذكار الصلاة وبعد السلام)
4. **Entering & Leaving the Mosque / Home** (أذكار دخول المسجد والخروج منه)
5. **Travel, Distress & Protection** (دعاء السفر والكرب والاستعاذة)
6. **Istikhara & Forgiveness** (دعاء الاستخارة والاستغفار)
