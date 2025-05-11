# axum

🌍 उपलब्ध भाषाएँ:  
🇺🇸 [English](README.md) | 🇷🇺 [Russian](README.ru.md) | 🇨🇳 [Chinese](README.zh.md) | 🇸🇦 [Arabic](README.ar.md) | 🇮🇳 [Hindi](README.hi.md) | 🇯🇵 [Japanese](README.ja.md)

`axum` एक वेब एप्लिकेशन फ्रेमवर्क है जो उपयोग में आसान और मॉड्यूलर है।

[![Build Status](https://github.com/tokio-rs/axum/actions/workflows/CI.yml/badge.svg?branch=main)](https://github.com/tokio-rs/axum/actions/workflows/CI.yml)
[![Crates.io](https://img.shields.io/crates/v/axum)](https://crates.io/crates/axum)
[![Documentation](https://docs.rs/axum/badge.svg)](https://docs.rs/axum)

इस क्रेट के बारे में अधिक जानकारी के लिए, कृपया [क्रेट डॉक्यूमेंटेशन][docs] देखें।

## प्रमुख विशेषताएँ

- बिना मैक्रो का उपयोग करते हुए सरल API द्वारा अनुरोधों को हैंडलर फ़ंक्शंस में रूट करना।
- रीक्वेस्ट को एक्सट्रैक्ट करने के लिए डिक्लेरेटिव तरीके से एक्स्ट्रैक्टर्स।
- एक सरल और भविष्यवाणी योग्य त्रुटि हैंडलिंग मॉडल।
- न्यूनतम बायलरप्लेट कोड के साथ प्रतिक्रियाएँ उत्पन्न करना।
- पूरी तरह से [`tower`] और [`tower-http`] इकोसिस्टम के मध्यवेयर, सेवाओं और उपकरणों का उपयोग करना।

अंतिम बिंदु विशेष रूप से `axum` को अन्य फ्रेमवर्क्स से अलग करता है।
`axum` में अपनी खुद की मध्यवेयर प्रणाली नहीं है, बल्कि यह [`tower::Service`] का उपयोग करता है। इसका मतलब यह है कि `axum` टाइमआउट्स, ट्रैकिंग, कंप्रेशन, प्रमाणीकरण आदि जैसी सुविधाओं को निःशुल्क प्रदान करता है, और इसके साथ ही [`hyper`] और [`tonic`] द्वारा लिखे गए एप्लिकेशनों के साथ मध्यवेयर साझा किया जा सकता है।

## ⚠️ ब्रेकिंग चेंजेस ⚠️

हम `axum` संस्करण 0.9 की ओर बढ़ रहे हैं, और `main` ब्रांच में ब्रेकिंग चेंजेस हो सकते हैं। क्रेट्स.आईओ पर उपलब्ध संस्करण के लिए कृपया [`0.8.x`] ब्रांच देखें।

[`0.8.x`]: https://github.com/tokio-rs/axum/tree/v0.8.x

## उपयोग का उदाहरण

```rust
use axum::{
    routing::{get, post},
    http::StatusCode,
    Json, Router,
};
use serde::{Deserialize, Serialize};

#[tokio::main]
async fn main() {
    // ट्रेसिंग को इनिशियलाइज करें
    tracing_subscriber::fmt::init();

    // एप्लिकेशन को सेटअप करें और रूटिंग जोड़ें
    let app = Router::new()
        // `GET /` के लिए `root` हैंडलर सेट करें
        .route("/", get(root))
        // `POST /users` के लिए `create_user` हैंडलर सेट करें
        .route("/users", post(create_user));

    // हाइपर का उपयोग करके एप्लिकेशन को 3000 पोर्ट पर शुरू करें
    let listener = tokio::net::TcpListener::bind("0.0.0.0:3000").await.unwrap();
    axum::serve(listener, app).await.unwrap();
}

// एक साधारण हैंडलर फ़ंक्शन जो एक स्थैतिक स्ट्रिंग लौटाता है
async fn root() -> &'static str {
    "Hello, World!"
}

async fn create_user(
    // यह तर्क `axum` को रीक्वेस्ट बॉडी को पार्स करके `CreateUser` प्रकार में बदलने की अनुमति देता है
    Json(payload): Json<CreateUser>,
) -> (StatusCode, Json<User>) {
    // एप्लिकेशन की लॉजिक यहाँ करें
    let user = User {
        id: 1337,
        username: payload.username,
    };

    // JSON प्रतिक्रिया और `201 Created` स्थिति को वापस करें
    (StatusCode::CREATED, Json(user))
}

// `create_user` हैंडलर का इनपुट डेटा
#[derive(Deserialize)]
struct CreateUser {
    username: String,
}

// `create_user` हैंडलर का आउटपुट डेटा
#[derive(Serialize)]
struct User {
    id: u64,
    username: String,
}
```

```rust

इस [example][readme-example] और अन्य उदाहरण परियोजनाओं में आप इस कोड का उपयोग देख सकते हैं।

अधिक उदाहरणों के लिए, कृपया [क्रेट डॉक्यूमेंटेशन][docs] देखें।

## प्रदर्शन

`axum` एक बहुत हल्का फ्रेमवर्क है, जो `hyper` पर आधारित है, और इसका ओवरहेड लगभग नगण्य है। इसका मतलब है कि `axum` का प्रदर्शन `hyper` के समान है। आप [यहां](https://github.com/programatik29/rust-web-benchmarks) और [यहां](https://web-frameworks-benchmark.netlify.app/result?l=rust) प्रदर्शन माप देख सकते हैं।

## सुरक्षा

यह क्रेट `#![forbid(unsafe_code)]` का उपयोग करता है, इसका मतलब है कि इसमें सभी कार्यान्वयन 100% सुरक्षित रस्ट कोड हैं।

## न्यूनतम समर्थित रस्ट संस्करण

axum का न्यूनतम समर्थित रस्ट संस्करण 1.75 है।

## उदाहरण

[examples] फ़ोल्डर में `axum` के विभिन्न उपयोगों के उदाहरण हैं। डॉक्यूमेंटेशन में भी कई कोड स्निपेट्स और उदाहरण हैं। यदि आप पूर्ण उदाहरण देखना चाहते हैं, तो समुदाय द्वारा बनाए गए \[Showcase प्रोजेक्ट] और \[ट्यूटोरियल्स] देखें।

## मदद चाहिए?

`axum` के रिपोजिटरी में सभी घटकों को जोड़ने के लिए कई उदाहरण हैं। आप [Discord चैट][chat] पर सवाल पूछ सकते हैं या [Discussion][discussion] में पोस्ट कर सकते हैं।

## सामुदायिक प्रोजेक्ट्स

[यहां][ecosystem] आप उन क्रेट्स और प्रोजेक्ट्स की सूची पा सकते हैं जो समुदाय द्वारा बनाए गए हैं और `axum` का उपयोग करते हैं।

## योगदान

🎉 आपकी मदद का स्वागत है! हम आपके योगदान का स्वागत करते हैं! `axum` प्रोजेक्ट में योगदान करने के लिए [Contribution Guide][contributing] देखें।

## लाइसेंस

यह प्रोजेक्ट [MIT लाइसेंस][license] के तहत जारी किया गया है।

### योगदान

जब तक आप स्पष्ट रूप से यह नहीं बताते, `axum` में सभी योगदान MIT लाइसेंस के तहत अनुमति प्राप्त हैं, और इसमें कोई अतिरिक्त शर्तें नहीं हैं।

[readme-example]: https://github.com/tokio-rs/axum/tree/main/examples/readme
[examples]: https://github.com/tokio-rs/axum/tree/main/examples
[docs]: https://docs.rs/axum
[`tower`]: https://crates.io/crates/tower
[`hyper`]: https://crates.io/crates/hyper
[`tower-http`]: https://crates.io/crates/tower-http
[`tonic`]: https://crates.io/crates/tonic
[contributing]: https://github.com/tokio-rs/axum/blob/main/CONTRIBUTING.md
[chat]: https://discord.gg/tokio
[discussion]: https://github.com/tokio-rs/axum/discussions/new?category=q-a
[`tower::Service`]: https://docs.rs/tower/latest/tower/trait.Service.html
[ecosystem]: https://github.com/tokio-rs/axum/blob/main/ECOSYSTEM.md
[showcases]: https://github.com/tokio-rs/axum/blob/main/ECOSYSTEM.md#project-showcase
[tutorials]: https://github.com/tokio-rs/axum/blob/main/ECOSYSTEM.md#tutorials
[license]: https://github.com/tokio-rs/axum/blob/main/axum/LICENSE

```
