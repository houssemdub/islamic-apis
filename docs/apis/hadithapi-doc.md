# HadithAPI.com REST Integration Guide

Developer documentation for integrating **HadithAPI.com**, a structured multi-book Hadith REST service offering authentication, multilingual search, and authenticity grading.

---

## 1. Overview

HadithAPI.com is an organized API service providing access to canonical collections with search capabilities across Arabic, English, and Urdu.

- **Base URL:** `https://hadithapi.com/api`
- **Authentication:** Query parameter `?apiKey={YOUR_API_KEY}`
- **Registration:** Free API keys generated immediately upon signup on [hadithapi.com](https://hadithapi.com).
- **Format:** JSON

---

## 2. Authentication

Append your API key to every request query string:
```text
https://hadithapi.com/api/books?apiKey=$HADITH_API_KEY
```

---

## 3. Endpoints

### 3.1 List All Books (`GET /books`)
```bash
curl "https://hadithapi.com/api/books?apiKey=YOUR_KEY"
```

**Supported Canonical Books & Slugs:**
- `sahih-bukhari` (Sahih al-Bukhari)
- `sahih-muslim` (Sahih Muslim)
- `al-tirmidhi` (Jami` at-Tirmidhi)
- `abu-dawood` (Sunan Abi Dawud)
- `sunan-nasai` (Sunan an-Nasa'i)
- `ibn-e-majah` (Sunan Ibn Majah)
- `mishkat` (Mishkat al-Masabih)

### 3.2 List Chapters of a Book (`GET /{bookSlug}/chapters`)
```bash
curl "https://hadithapi.com/api/sahih-bukhari/chapters?apiKey=YOUR_KEY"
```

### 3.3 Search & Filter Hadiths (`GET /hadiths`)
Filter by book, chapter number, grading authenticity, or query terms:

```bash
# Search for hadiths regarding fasting in Bukhari
curl "https://hadithapi.com/api/hadiths?apiKey=YOUR_KEY&book=sahih-bukhari&hadithEnglish=fasting&paginate=10"

# Fetch Sahih Bukhari Hadith #1
curl "https://hadithapi.com/api/hadiths?apiKey=YOUR_KEY&book=sahih-bukhari&hadithNumber=1"
```

**Filter Parameters:**
- `book`: Book slug (`sahih-bukhari`, etc.)
- `chapter`: Chapter index
- `hadithNumber`: Hadith number string
- `status`: Grade (`Sahih`, `Hasan`, `Da`eef`)
- `hadithEnglish`: English full-text search
- `hadithUrdu`: Urdu full-text search
- `hadithArabic`: Arabic text search
- `paginate`: Results per page (default: 25)
