# Nafs Guard — Remote Config নির্দেশনা

এই নির্দেশনা Android app-এর version control, উপরের marquee এবং নিচের promotional banner পরিচালনার জন্য। এই সুবিধাসহ build ফোনে install করা থাকতে হবে।

## ১. Live config কোথায় পরিবর্তন করবেন

- Repository: https://github.com/nazmulhasan77/App_Config
- Branch: `main`
- File: `NafsGuard/config.json`
- Edit link: https://github.com/nazmulhasan77/App_Config/blob/main/NafsGuard/config.json
- App যে URL পড়ে: https://raw.githubusercontent.com/nazmulhasan77/App_Config/main/NafsGuard/config.json

GitHub-এ file edit করে `main` branch-এ commit করুন। Local checkout ব্যবহার করলে commit এবং push করুন। Repository public হতে হবে। এই app repository-এর `App_Version_Control.json` শুধু local example; এখানে পরিবর্তন করলে ফোনের live config বদলাবে না।

## ২. সম্পূর্ণ config

```json
{
  "minimum_version": "1.0.3",
  "marquee": {
    "enabled": true,
    "text": "আত্মাকে কন্ট্রোল করুন",
    "click_url": ""
  },
  "advertisement": {
    "enabled": false,
    "id": "banner-001",
    "image_url": "",
    "click_url": "",
    "start_date": "",
    "end_date": ""
  }
}
```

এই উদাহরণে marquee চালু, banner বন্ধ। Banner চালাতে নিজের সরাসরি public image URL দিয়ে `advertisement.enabled`-কে `true` করুন।

JSON লেখার নিয়ম:

- Key ও string-এর জন্য double quote ব্যবহার করুন। Text-এর ভেতর double quote লাগলে `\"` লিখুন।
- `true` / `false` boolean; এগুলোর চারপাশে quote দেবেন না।
- শেষ property-এর পরে comma দেবেন না। JSON-এ comment লিখবেন না।
- বাংলা text UTF-8 হিসেবে save করুন।
- `minimum_version` ভুল বা অনুপস্থিত হলে পুরো নতুন config গ্রহণ করা হবে না।
- একটি feature বন্ধ করতে তার `enabled`-কে `false` করুন; অন্য section রাখুন।

## ৩. Version control ও বাধ্যতামূলক update

`minimum_version` হলো app ব্যবহার করতে দেওয়া সবচেয়ে পুরোনো version। উদাহরণ:

```json
"minimum_version": "1.0.4"
```

| Installed version | ফলাফল |
| --- | --- |
| `1.0.3` | বাধ্যতামূলক update screen |
| `1.0.4` | App খুলবে |
| `1.0.5` | App খুলবে |
| `1.0.10` | App খুলবে; numeric comparison হয় |

তিনটি numeric component দিন: `major.minor.patch`। `1.0.4` সঠিক; `v1.0.4`, `1.0`, `latest`, `1.0.4+1004` সঠিক নয়।

### pubspec.yaml-এর version

```yaml
version: 1.0.4+1004
```

এখানে `1.0.4` app version এবং `1004` build number। Config-এ শুধু `1.0.4` লিখবেন। বর্তমান checker build number তুলনা করে না; শুধু build number বাড়িয়ে একই app version রাখলে forced update হবে না। ফোনে actual installed version পড়া হয়; build command দিয়ে version override করলে সেটিই কার্যকর হবে।

### Release দেওয়ার ধাপ

1. `pubspec.yaml`-এ নতুন version ও build number দিন।
2. Google Play-এ নতুন release publish করুন।
3. যাদের update বাধ্যতামূলক করবেন তাদের জন্য release উপলব্ধ হয়েছে নিশ্চিত করুন।
4. তারপর public config-এ `minimum_version` বাড়ান।
5. কম version-এর ফোনে update screen এবং Play Store link পরীক্ষা করুন।

Update screen-এ skip button নেই। “Update now” প্রথমে Play Store app খোলে; সেটি ব্যর্থ হলে browser-এ listing খোলার চেষ্টা করে। দুটোই ব্যর্থ হলে error text দেখায়।

Listing-এর ID installed Android package name থেকে আসে। বর্তমান project-এর application ID `com.butterflydevs.nafsguard`; প্রকাশিত Play Store listing-এর ID মিলতে হবে। JSON-এ আলাদা Play Store URL field নেই।

ভুল করে minimum বেশি দিলে public config-এ কমিয়ে commit করুন। ফোনের পরবর্তী সফল check-এ বাধ্যতামূলক update উঠে যাবে। Offline থাকলে আগের cached নিয়ম চালু থাকতে পারে। এই checker ছাড়া পুরোনো build-কে JSON দিয়ে নতুন আচরণ দেওয়া যাবে না।

## ৪. উপরের marquee / announcement

| Field | কাজ |
| --- | --- |
| `marquee.enabled` | `true` চালু, `false` লুকানো |
| `marquee.text` | চলমান text; খালি হলে লুকানো |
| `marquee.click_url` | Optional HTTP/HTTPS link; খালি হলে tap action নেই |

```json
"marquee": {
  "enabled": true,
  "text": "নতুন আপডেট এসেছে! বিস্তারিত জানতে এখানে চাপুন।",
  "click_url": "https://www.facebook.com/nafsGuard/"
}
```

Home, Blocking, Statistics ও Settings—চারটি main tab-এর উপরে marquee থাকে। আলাদা full-screen page বা required-update screen-এ এটি দেখায় না।

একটি animation cycle প্রায় ১৮ সেকেন্ড। Speed, font ও color বদলানোর config field নেই। Device-এ reduced-motion চালু থাকলে static wrapping text দেখাবে।

## ৫. নিচের banner

| Field | কাজ |
| --- | --- |
| `advertisement.enabled` | Banner চালু/বন্ধ |
| `advertisement.id` | Campaign identifier; যেমন `banner-001`; খালি রাখা যায় |
| `advertisement.image_url` | সরাসরি public image URL; দেখাতে প্রয়োজন |
| `advertisement.click_url` | Tap করলে খোলার optional HTTP/HTTPS link |
| `advertisement.start_date` | কখন থেকে দেখাবে; খালি মানে শুরুর সীমা নেই |
| `advertisement.end_date` | কখন পর্যন্ত দেখাবে; খালি মানে শেষের সীমা নেই |

এটি config-controlled promotional image banner। `id` AdMob ad-unit ID নয়। Ad network SDK, আয় হিসাব বা impression/click tracking নেই। একবারে একটি banner দেখায়। চারটি main tab-এর bottom navigation-এর উপরে থাকে।

### ছবির resolution ও format

| বিষয় | নির্দেশনা |
| --- | --- |
| প্রস্তাবিত resolution | **1080 × 216 px** |
| প্রস্তাবিত ratio | **5:1** |
| Format | PNG বা WebP |
| প্রস্তাবিত file size | সম্ভব হলে **200 KB-এর নিচে** |
| App-এ উচ্চতা | **72 logical pixels** |
| App-এ প্রস্থ | পুরো screen |
| Image fit | `BoxFit.contain`; crop হবে না, অনুপাত অনুযায়ী ফাঁকা থাকতে পারে |

Resolution ও file size উপরেরগুলো recommendation; app এই সীমা বাধ্যতামূলকভাবে enforce করে না। ছোট display-তে পড়ার জন্য text বড় রাখুন, বেশি লেখা এড়িয়ে চলুন। Image load ব্যর্থ হলে banner লুকায়।

### Image upload ও URL

Public `App_Config` repository-তে নিজের ছবি `NafsGuard/banners/banner-001.png` হিসেবে upload করলে raw URL হবে:

```text
https://raw.githubusercontent.com/nazmulhasan77/App_Config/main/NafsGuard/banners/banner-001.png
```

এই path উদাহরণ; ব্যবহার করার আগে সত্যি সেখানে ছবি upload করতে হবে। `github.com/.../blob/...` page URL দেবেন না। Browser-এ image URL খুললে সরাসরি ছবি দেখা যেতে হবে, login লাগবে না।

```json
"advertisement": {
  "enabled": true,
  "id": "banner-001",
  "image_url": "https://raw.githubusercontent.com/nazmulhasan77/App_Config/main/NafsGuard/banners/banner-001.png",
  "click_url": "https://www.facebook.com/nafsGuard/",
  "start_date": "",
  "end_date": ""
}
```

নতুন ছবি দিলে নতুন filename/URL ব্যবহার করুন, যেমন `banner-002.png`, যাতে পুরোনো image cache নিয়ে বিভ্রান্তি না হয়।

## ৬. Banner schedule

### পুরো দিন ধরে schedule

```json
"start_date": "2026-09-08",
"end_date": "2026-09-30"
```

ফোনের local time অনুযায়ী ৮ সেপ্টেম্বর থেকে ৩০ সেপ্টেম্বরের পুরো দিন দেখাবে; ১ অক্টোবর থেকে বন্ধ। উদাহরণের তারিখ নিজের campaign অনুযায়ী বদলাবেন।

### নির্দিষ্ট সময় ও timezone

```json
"start_date": "2026-09-08T09:00:00+06:00",
"end_date": "2026-09-30T23:00:00+06:00"
```

বাংলাদেশ সময় সকাল ৯টায় শুরু এবং শেষ তারিখ রাত ১১টায় বন্ধ। Timestamp-এর end time exclusive: ঐ মুহূর্ত থেকেই banner লুকাবে।

- দুই date খালি হলে enabled banner সবসময় দেখাতে পারবে।
- শুধু start দিলে ঐ সময় থেকে; শুধু end দিলে ঐ সময় পর্যন্ত।
- Valid ISO date/time দিন এবং start যেন end-এর আগে হয়।
- Parse করা যায় না এমন date থাকলে banner লুকায়।
- Schedule ফোনের clock-এর ওপর নির্ভর করে; ভুল clock হলে সময়ও ভুল হতে পারে।
- Visibility প্রতি ৩০ সেকেন্ডে হিসাব হয়। এটি প্রতি ৩০ সেকেন্ডে remote config download করে না।

## ৭. Refresh, cache ও offline আচরণ

- App launch এবং background থেকে ফিরে এলে config check হয়।
- App খোলা রেখে দিলে remote edit সঙ্গে সঙ্গে push হয় না; background করে ফিরে আসুন বা আবার খুলুন।
- Network request timeout ৮ সেকেন্ড।
- সর্বশেষ valid config cache হয়। Fetch ব্যর্থ হলে cached version, marquee ও banner configuration ব্যবহার হয়।
- Cache না থাকলে fetch failure-এর সময় app খুলবে, promotional content দেখাবে না।
- Config cached থাকলেও banner image offline-এ পাওয়া নিশ্চিত নয়; image load ব্যর্থ হলে লুকায়।
- Invalid remote JSON নতুন config হিসেবে গ্রহণ হয় না; আগের valid config থাকে।
- Required-update gate app UI আটকে রাখে; Android background blocking services-এর lifecycle আলাদা।

## ৮. Troubleshooting

| সমস্যা | কী দেখবেন |
| --- | --- |
| Config change কাজ করছে না | Public `App_Config/main/NafsGuard/config.json`-এ commit হয়েছে কি না; raw URL ও internet ঠিক আছে কি না; app আবার খুলুন |
| Update screen নেই | Installed version minimum-এর নিচে কি না, checker-সহ build আছে কি না, JSON valid কি না |
| অপ্রয়োজনীয় update চাইছে | Minimum বেশি দেওয়া হয়েছে কি না; কমানোর পরে ফোন নতুন config পেয়েছে কি না |
| Play Store listing নেই | Published application ID ও installed package ID মিলছে কি না |
| Marquee নেই | Boolean `enabled: true` এবং nonempty `text` আছে কি না |
| Banner নেই | Enabled, direct public image URL, schedule ও device clock পরীক্ষা করুন |
| Link খুলছে না | `click_url` খালি/invalid কি না; HTTP/HTTPS URL দিন |
| পুরোনো ছবি | নতুন filename/URL দিন এবং config refresh করুন |

## ৯. Developer: host পরিবর্তন ও tests

Default URL: `lib/services/app_update_service.dart`। অন্য HTTPS host ব্যবহার করতে build-এর সময় দিন:

```sh
flutter build appbundle --release --dart-define=APP_VERSION_CONFIG_URL=https://your-host/config.json
```

`your-host` নিজের actual host দিয়ে বদলাবেন। এটি build-time setting; নতুন URL installed ফোনে দিতে updated build প্রয়োজন।

| File | কাজ |
| --- | --- |
| `lib/services/app_update_service.dart` | Fetch, version comparison ও cache |
| `lib/models/remote_app_config.dart` | Config fields ও banner schedule |
| `lib/screens/app_update_gate.dart` | Update screen ও Play Store action |
| `lib/widgets/remote_promotions.dart` | Marquee এবং banner UI |
| `lib/screens/home_screen.dart` | Main tabs-এ placement |

Tests:

```sh
flutter test test/app_update_service_test.dart test/remote_app_config_test.dart test/remote_promotions_test.dart
```

Release-এর আগে ফোনে lower/equal/newer version, offline fallback, marquee link, banner image/link এবং schedule boundary পরীক্ষা করুন।
