# PrayerZone & Algerian Prayer Time Calculation — Integration Guide

The **PrayerZone** Flutter/Dart library ([pub.dev/packages/prayer_zone](https://pub.dev/packages/prayer_zone), [API Documentation](https://pub.dev/documentation/prayer_zone/latest/)) provides astronomical prayer time calculation methods and geolocation defaults across 182 countries, supporting 23 official calculation methods and 2 juristic Madhabs.

This guide provides complete technical specifications for the **Algerian Prayer Time Calculation** (*وزارة الشؤون الدينية والأوقاف الجزائرية*) as defined in `prayer_zone`, along with production-ready Flutter/Dart, TypeScript, and Python implementations.

---

## Quick Reference Summary

| Property | Details |
| :--- | :--- |
| **Package** | `prayer_zone` (Dart / Flutter) |
| **Pub.dev URL** | [`https://pub.dev/packages/prayer_zone`](https://pub.dev/packages/prayer_zone) |
| **Documentation** | [`https://pub.dev/documentation/prayer_zone/latest/`](https://pub.dev/documentation/prayer_zone/latest/) |
| **Calculation Method** | `PrayerCalculationMethod.algerian` |
| **Authority** | **Algerian Ministry of Religious Affairs and Wakfs** (*وزارة الشؤون الدينية والأوقاف*) |
| **Fajr Angle** | **`18.0°`** (Astronomical depression angle) |
| **Isha Angle** | **`17.0°`** (Astronomical depression angle) |
| **Default Madhab** | `PrayerMadhab.shafi` (Shadow factor = 1:1, standard for the Maliki school in Algeria) |
| **ISO Country Code** | `DZ` (`Algeria`) |
| **Supported Countries** | 182 countries |
| **Total Methods** | 23 official calculation authorities |

---

## 1. Algerian Prayer Time Calculation Specification

In Algeria, official prayer timetables issued by the **Ministry of Religious Affairs and Wakfs** rely on the following astronomical standards:

```text
┌──────────────────────┬─────────────┬────────────────────────────────────────────────────────┐
│ Prayer               │ Method /    │ Astronomical Rule                                      │
│                      │ Angle       │                                                        │
├──────────────────────┼─────────────┼────────────────────────────────────────────────────────┤
│ Fajr (الفجر)         │ 18.0°       │ Sun reaches 18.0° below eastern horizon                │
│ Sunrise (الشروق)     │ 0.833°      │ Sun's upper limb touches horizon (refraction + radius) │
│ Dhuhr (الظهر)        │ Solar Noon  │ Sun transits local celestial meridian + 1-2 min buffer │
│ Asr (العصر)          │ Ratio 1:1   │ Shadow length = object length + noon shadow (Maliki)   │
│ Maghrib (المغرب)     │ Sunset      │ Complete disappearance of sun below western horizon    │
│ Isha (العشاء)        │ 17.0°       │ Sun reaches 17.0° below western horizon                │
└──────────────────────┴─────────────┴────────────────────────────────────────────────────────┘
```

> [!NOTE]
> **Juristic School (Fiqh):** The national religious authority in Algeria adheres to the **Maliki school of jurisprudence**. In prayer time calculations, the Maliki standard for Asr is identical to Shafi and Hanbali (shadow length equals object height plus solar noon shadow length). In `prayer_zone` and API parameters, use `shafi` or `0` for the standard Maliki shadow length.

---

## 2. All 23 Calculation Methods in PrayerZone

The `prayer_zone` library catalogs 23 global calculation conventions:

| Method Key | Authority / Convention | Fajr Angle | Isha Angle / Interval |
| :--- | :--- | :--- | :--- |
| **`algerian`** | **Algerian Ministry of Religious Affairs and Wakfs** | **`18.0°`** | **`17.0°`** |
| `muslim_world_league` | Muslim World League (MWL) | `18.0°` | `17.0°` |
| `egyptian` | Egyptian General Authority of Survey | `19.5°` | `17.5°` |
| `french` | Union des Organisations Islamiques de France (UOIF) | `12.0°` | `12.0°` |
| `gulf_region` | Gulf Region Convention | `19.5°` | 90 min interval |
| `indonesian` | Kementerian Agama Republik Indonesia (KEMENAG) | `20.0°` | `18.0°` |
| `jafari` | Shia Ithna-Ashari, Leva Institute, Qum | `16.0°` | `14.0°` (Maghrib: 4.0°) |
| `jordan` | Ministry of Awqaf, Jordan | `18.0°` | `18.0°` (+5 min Maghrib) |
| `karachi` | University of Islamic Sciences, Karachi | `18.0°` | `18.0°` |
| `umm_al_qura` | Umm al-Qura University, Makkah | `18.5°` | 90 min interval |
| `dubai` | UAE Convention | `18.2°` | `18.2°` (+3 min offsets) |
| `portugal` | Comunidade Islamica de Lisboa | `18.0°` | 77 min interval |
| `qatar` | Qatar Convention | `18.0°` | 90 min interval |
| `kuwait` | Kuwait Convention | `18.0°` | `17.5°` |
| `moonsighting_committee` | Moonsighting Committee Worldwide | `18.0°` | `18.0°` |
| `moroccon` | Moroccan Ministry of Habous and Islamic Affairs | `19.0°` | `17.0°` |
| `russian` | Spiritual Administration of Muslims of Russia | `16.0°` | `15.0°` |
| `singapore` | Majlis Ugama Islam Singapura (MUIS) | `20.0°` | `18.0°` (+1 min Dhuhr) |
| `tunisian` | Tunisian Ministry of Religious Affairs | `18.0°` | `18.0°` |
| `turkey` | Diyanet İşleri Başkanlığı (Turkey) | Approximation | Approximation |
| `tehran` | Institute of Geophysics, University of Tehran | `17.7°` | `14.0°` (Maghrib: 4.5°) |
| `north_america` | Islamic Society of North America (ISNA) | `15.0°` | `15.0°` |
| `jakim` | JAKIM / Custom Base | `0.0°` | `0.0°` |

---

## 3. Flutter & Dart Implementation

### Installation

Add `prayer_zone` to your Flutter or Dart project's `pubspec.yaml`:

```yaml
dependencies:
  prayer_zone: ^0.0.7
```

Then run:

```bash
flutter pub get
```

### Retrieving Algerian Method & Country Preset

```dart
import 'package:prayer_zone/prayer_zone.dart';

void main() {
  // 1. Fetch Algeria country profile by ISO code ('DZ')
  final algeria = PrayerZone.getCountry('DZ');
  print('Country: ${algeria.name}');             // Algeria
  print('Country Code: ${algeria.code}');        // DZ
  print('Method: ${algeria.prayerMethod}');      // PrayerCalculationMethod.algerian
  print('Madhab: ${algeria.prayerMadhab}');      // PrayerMadhab.shafi

  // 2. Inspect Algerian Ministry calculation parameters
  final algerianMethod = PrayerZone.aboutCalculationMethod(PrayerCalculationMethod.algerian);
  print('Fajr Angle: ${algerianMethod.fajrAngel}°');   // 18.0°
  print('Isha Angle: ${algerianMethod.ishaAngel}°');   // 17.0°

  // 3. Inspect all 182 supported countries
  print('Total supported countries: ${PrayerZone.supportedCountriesCount}');
}
```

---

## 4. Pure TypeScript / JavaScript Implementation

Drop-in mathematical calculation of Algerian prayer times (Fajr `18.0°`, Isha `17.0°`, Maliki Asr factor `1`):

```typescript
/**
 * Calculates Algerian prayer times using the Algerian Ministry of Religious Affairs standard:
 * Fajr: 18.0°, Isha: 17.0°, Asr shadow factor: 1 (Maliki/Shafi)
 */
export interface PrayerTimes {
  fajr: string;
  sunrise: string;
  dhuhr: string;
  asr: string;
  maghrib: string;
  isha: string;
}

export function calculateAlgerianPrayerTimes(
  latitude: number,
  longitude: number,
  timezoneOffsetHours: number,
  date: Date = new Date()
): PrayerTimes {
  const rad = Math.PI / 180;
  const deg = 180 / Math.PI;

  // Day of year
  const startOfYear = new Date(date.getFullYear(), 0, 0);
  const diff = date.getTime() - startOfYear.getTime();
  const dayOfYear = Math.floor(diff / (1000 * 60 * 60 * 24));

  // Solar declination and equation of time approximations
  const b = (2 * Math.PI * (dayOfYear - 81)) / 365;
  const eot = 9.87 * Math.sin(2 * b) - 7.53 * Math.cos(b) - 1.5 * Math.sin(b); // minutes
  const declination = 23.45 * Math.sin((360 / 365) * (dayOfYear - 81) * rad); // degrees

  // Solar noon in local hours
  const solarNoon = 12 + timezoneOffsetHours - longitude / 15 - eot / 60;

  // Hour angle helper for sun depression angle
  function hourAngle(altitudeAngle: number): number {
    const latRad = latitude * rad;
    const decRad = declination * rad;
    const altRad = altitudeAngle * rad;
    const cosHA = (Math.sin(altRad) - Math.sin(latRad) * Math.sin(decRad)) /
                  (Math.cos(latRad) * Math.cos(decRad));
    if (cosHA > 1 || cosHA < -1) return NaN;
    return Math.acos(cosHA) * deg;
  }

  // Algerian Ministry Angles
  const fajrHA = hourAngle(-18.0);              // Fajr: 18° depression
  const sunriseHA = hourAngle(-0.833);          // Sunrise: 50 arcminutes
  const ishaHA = hourAngle(-17.0);              // Isha: 17° depression

  // Asr hour angle (Maliki / Shafi shadow factor = 1)
  const noonSunAltitude = 90 - Math.abs(latitude - declination);
  const asrAltitude = Math.atan(1 / (1 + Math.tan((90 - noonSunAltitude) * rad))) * deg;
  const asrHA = hourAngle(asrAltitude);

  function formatTime(hours: number): string {
    const totalMinutes = Math.round(hours * 60);
    const h = Math.floor(totalMinutes / 60) % 24;
    const m = totalMinutes % 60;
    return `${String(h).padStart(2, '0')}:${String(m).padStart(2, '0')}`;
  }

  return {
    fajr: formatTime(solarNoon - fajrHA / 15),
    sunrise: formatTime(solarNoon - sunriseHA / 15),
    dhuhr: formatTime(solarNoon + (2 / 60)), // 2-minute safety after zenith
    asr: formatTime(solarNoon + asrHA / 15),
    maghrib: formatTime(solarNoon + sunriseHA / 15),
    isha: formatTime(solarNoon + ishaHA / 15),
  };
}

// Example: Algiers (latitude 36.7538, longitude 3.0588, UTC+1)
const timings = calculateAlgerianPrayerTimes(36.7538, 3.0588, 1, new Date());
console.log('Algiers Prayer Times:', timings);
```

---

## 5. Pure Python Implementation

```python
import math
from datetime import date, datetime

def calculate_algerian_prayer_times(latitude: float, longitude: float, timezone_offset: float, target_date: date = None):
    """
    Calculates prayer times for Algerian locations using Algerian Ministry specifications:
    - Fajr: 18.0° depression
    - Isha: 17.0° depression
    - Asr: Maliki / Shafi standard shadow factor (1)
    """
    if target_date is None:
        target_date = date.today()

    day_of_year = target_date.timetuple().tm_yday
    b = 2 * math.pi * (day_of_year - 81) / 365
    eot = 9.87 * math.sin(2 * b) - 7.53 * math.cos(b) - 1.5 * math.sin(b) # minutes
    declination = 23.45 * math.sin(math.radians((360 / 365) * (day_of_year - 81))) # degrees

    solar_noon = 12 + timezone_offset - (longitude / 15.0) - (eot / 60.0)

    def hour_angle(altitude_deg: float) -> float:
        lat_rad = math.radians(latitude)
        dec_rad = math.radians(declination)
        alt_rad = math.radians(altitude_deg)
        cos_ha = (math.sin(alt_rad) - math.sin(lat_rad) * math.sin(dec_rad)) / (
            math.cos(lat_rad) * math.cos(dec_rad)
        )
        return math.degrees(math.acos(cos_ha))

    fajr_ha = hour_angle(-18.0)
    sunrise_ha = hour_angle(-0.833)
    isha_ha = hour_angle(-17.0)

    noon_altitude = 90 - abs(latitude - declination)
    asr_altitude = math.degrees(math.atan(1.0 / (1.0 + math.tan(math.radians(90 - noon_altitude)))))
    asr_ha = hour_angle(asr_altitude)

    def to_hhmm(hours: float) -> str:
        mins = round(hours * 60)
        h = (mins // 60) % 24
        m = mins % 60
        return f"{h:02d}:{m:02d}"

    return {
        "Fajr": to_hhmm(solar_noon - fajr_ha / 15.0),
        "Sunrise": to_hhmm(solar_noon - sunrise_ha / 15.0),
        "Dhuhr": to_hhmm(solar_noon + (2.0 / 60.0)),
        "Asr": to_hhmm(solar_noon + asr_ha / 15.0),
        "Maghrib": to_hhmm(solar_noon + sunrise_ha / 15.0),
        "Isha": to_hhmm(solar_noon + isha_ha / 15.0),
    }

# Algiers, Algeria (Lat: 36.7538, Lng: 3.0588, Timezone: UTC+1)
times = calculate_algerian_prayer_times(36.7538, 3.0588, 1.0)
print(times)
```

---

## 6. Querying via REST APIs (AlAdhan & UmmahAPI)

If you are calling cloud REST APIs instead of offline Flutter/Dart client libraries:

### AlAdhan API (Method `19`)
AlAdhan assigns ID `19` to the **Algerian Ministry of Religious Affairs and Wakfs**:

```bash
# Query by City and Country
curl -X GET "https://api.aladhan.com/v1/timingsByCity?city=Algiers&country=Algeria&method=19"

# Query by Coordinates (Algiers: 36.7538, 3.0588)
curl -X GET "https://api.aladhan.com/v1/timings?latitude=36.7538&longitude=3.0588&method=19"
```

### UmmahAPI (`method=Algeria`)
UmmahAPI accepts `method=Algeria` or `method=19`:

```bash
curl -X GET "https://ummahapi.com/api/prayer-times?lat=36.7538&lng=3.0588&method=Algeria&madhab=Shafi"
```
