# Fawaz Ahmed Quran REST API Integration Guide

Comprehensive documentation for integrating the fast, free, CDN-hosted **Fawaz Ahmed Quran API**.

---

## 1. Overview

The Fawaz Ahmed Quran API provides instantaneous access to the Qur'anic text in original Uthmani script along with 440+ translations and transliterations spanning more than 90 languages.

- **Primary CDN:** `https://cdn.jsdelivr.net/gh/fawazahmed0/quran-api@1`
- **Fallback Base:** `https://raw.githubusercontent.com/fawazahmed0/quran-api/1`
- **Authentication:** None (Public / Keyless)
- **Rate Limit:** Unlimited edge CDN distribution

---

## 2. Endpoints & Examples

### 2.1 List All Editions (`GET /editions.json`)
```bash
curl "https://cdn.jsdelivr.net/gh/fawazahmed0/quran-api@1/editions.json"
```

### 2.2 Get Surah in a Translation
```bash
# Get Surah Al-Fatihah (1) in English (Abdel Haleem translation)
curl "https://cdn.jsdelivr.net/gh/fawazahmed0/quran-api@1/editions/eng-abdelhaleem/1.json"

# In Uthmani Arabic Script
curl "https://cdn.jsdelivr.net/gh/fawazahmed0/quran-api@1/editions/ara-quranuthmani/1.json"
```

### 2.3 Get Specific Ayah
```bash
# Chapter 2 (Al-Baqarah), Verse 255 (Ayat al-Kursi)
curl "https://cdn.jsdelivr.net/gh/fawazahmed0/quran-api@1/editions/eng-abdelhaleem/2/255.json"
```
