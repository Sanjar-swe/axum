# axum

🌍 Доступные языки:  
🇺🇸 [English](README.md) | 🇷🇺 [Русский](README.ru.md) | 🇨🇳 [中文](README.zh.md) | 🇸🇦 [العربية](README.ar.md) | 🇮🇳 [हिन्दी](README.hi.md) | 🇯🇵 [日本語](README.ja.md)

`axum` — это фреймворк для веб-приложений, ориентированный на эргономику и модульность.

[![Статус сборки](https://github.com/tokio-rs/axum/actions/workflows/CI.yml/badge.svg?branch=main)](https://github.com/tokio-rs/axum/actions/workflows/CI.yml)
[![Crates.io](https://img.shields.io/crates/v/axum)](https://crates.io/crates/axum)
[![Документация](https://docs.rs/axum/badge.svg)][docs]

Дополнительную информацию об этом пакете можно найти в [документации на crates.io][docs].

## Основные возможности

- Маршрутизация запросов к обработчикам с использованием API без макросов.
- Декларативный парсинг запросов с использованием экстракторов.
- Простая и предсказуемая модель обработки ошибок.
- Генерация ответов с минимальной шаблонной кодировкой.
- Полная интеграция с экосистемой [`tower`] и [`tower-http`], включая промежуточное ПО, сервисы и утилиты.

Особенно стоит отметить последний пункт, который выделяет `axum` среди других фреймворков. `axum` не имеет собственной системы промежуточного ПО, а вместо этого использует [`tower::Service`]. Это означает, что `axum` автоматически предоставляет такие функции, как тайм-ауты, трассировка, сжатие, авторизация и многое другое. Также это позволяет легко интегрировать промежуточное ПО с приложениями, использующими [`hyper`] или [`tonic`].

## ⚠ Несовместимые изменения ⚠

Мы работаем над версией `axum` 0.9, поэтому ветка `main` может содержать несовместимые изменения. Для стабильных релизов смотрите ветку [`0.8.x`] на crates.io.

[`0.8.x`]: https://github.com/tokio-rs/axum/tree/v0.8.x

## Пример использования

````rust
use axum::{
    routing::{get, post},
    http::StatusCode,
    Json, Router,
};
use serde::{Deserialize, Serialize};

#[tokio::main]
async fn main() {
    // инициализация трассировки
    tracing_subscriber::fmt::init();

    // создание приложения с маршрутом
    let app = Router::new()
        // `GET /` направляется к `root`
        .route("/", get(root))
        // `POST /users` направляется к `create_user`
        .route("/users", post(create_user));

    // запуск приложения с hyper, слушающего на порту 3000
    let listener = tokio::net::TcpListener::bind("0.0.0.0:3000").await.unwrap();
    axum::serve(listener, app).await.unwrap();
}

// базовый обработчик, который отвечает статической строкой
async fn root() -> &'static str {
    "Hello, World!"
}

async fn create_user(
    // этот аргумент говорит axum парсить тело запроса
    // как JSON в тип `CreateUser`
    Json(payload): Json<CreateUser>,
) -> (StatusCode, Json<User>) {
    // добавьте вашу логику приложения здесь
    let user = User {
        id: 1337,
        username: payload.username,
    };

    // это будет преобразовано в JSON-ответ
    // со статусом `201 Created`
    (StatusCode::CREATED, Json(user))
}

// входные данные для нашего обработчика `create_user`
#[derive(Deserialize)]
struct CreateUser {
    username: String,
}

// выходные данные для нашего обработчика `create_user`
#[derive(Serialize)]
struct User {
    id: u64,
    username: String,
}
```rust

````

Этот пример, а также другие примеры проектов можно найти в [директории примеров][examples].

Дополнительные примеры можно найти в [документации][docs].

## Производительность

`axum` — это достаточно тонкий слой поверх [`hyper`], добавляющий минимальные накладные расходы. Таким образом, производительность `axum` сопоставима с производительностью [`hyper`]. Сравнительные тесты можно найти [здесь](https://github.com/programatik29/rust-web-benchmarks) и [здесь](https://web-frameworks-benchmark.netlify.app/result?l=rust).

## Безопасность

Этот пакет использует `#![forbid(unsafe_code)]`, чтобы гарантировать, что весь код написан в 100% безопасном Rust.

## Минимальная поддерживаемая версия Rust

Минимальная поддерживаемая версия Rust для axum — 1.75.

## Примеры

Папка [examples] содержит различные примеры использования `axum`. Также в \[документации] приведено множество фрагментов кода и примеров. Для полноценных примеров ознакомьтесь с [showcases] или [tutorials].

## Получение помощи

В репозитории `axum` есть [несколько примеров][examples], демонстрирующих, как собрать всё вместе. Также существуют сообщества, поддерживающие проекты, такие как [showcases] и [tutorials], которые показывают, как использовать `axum` для реальных приложений. Вы также можете задать вопросы в [канале Discord][chat] или открыть \[обсуждение] на GitHub.

## Сообщественные проекты

См. [здесь][ecosystem] для списка сообществом поддерживаемых пакетов и проектов, созданных с использованием `axum`.

## Как внести вклад

🎈 Спасибо за помощь в улучшении проекта! Мы очень рады, что вы с нами! У нас есть [руководство для контрибьюторов][contributing], которое поможет вам стать частью проекта `axum`.

## Лицензия

Этот проект лицензирован по лицензии [MIT][license].

### Вклад

Если вы намеренно отправляете вклад для включения в проект `axum`, ваш вклад будет лицензирован по лицензии MIT, без дополнительных условий или ограничений.

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
