# INCY на macOS: 43 сервиса + реестр РКН через VPN, всё остальное — без

> 🇬🇧 **English version:** [README-EN.md](README-EN.md)

## ⚖️ Правовая оговорка (прочитай сначала)

Материал носит **информационно-технический характер**: это личный гайд по настройке клиента INCY (профиль маршрутизации) на macOS. Он **не является рекламой и не продвигает** ни один VPN-сервис, не содержит призывов к его использованию и не предоставляет доступа к сторонним сервисам.

Автору известно, что законодательство РФ регулирует распространение информации о средствах обхода блокирования:
- **8-ФЗ «Об информации, информационных технологиях и о защите информации»** (ст. 15) — блокировка страниц с «информацией о способах, методах обеспечения доступа к информационным ресурсам, доступ к которым ограничен на территории РФ»;
- **Приказ Роскомнадзора от 17.10.2024 № 196** — критерии запрещённой информации, включая научно-технические данные о VPN-сервисах, используемых для обхода блокировок;
- **281-ФЗ от 31.07.2025** — поправки в КоАП РФ: реклама VPN-сервисов (ст. 14.2) и умышленный поиск экстремистских материалов (ст. 13.53);
- **282-ФЗ от 31.07.2025** — использование средств обхода блокирования при совершении преступления является отягчающим обстоятельством (ст. 63 УК РФ).

Автор **не призывает к обходу блокировок**, установленных в соответствии с законодательством РФ. Использование материала — на усмотрение и риск читателя, в соответствии с действующим законодательством РФ. По состоянию на дату публикации сам факт использования VPN физическим лицом административной ответственностью в КоАП РФ не предусмотрен. Текст не является юридической консультацией; за актуальным мнением обращайтесь к юристу.

**На Mac профиль ставится ссылкой** (она в [Шаге 3](#шаг-3-добавь-профиль-маршрутизации-ключевой-шаг)): открой её в Safari — macOS предложит открыть INCY.

> 📎 **QR-кода в этом репозитории нет.** С профилем на 61 правило ссылка занимает 2916 символов при пределе QR 2953 — впритык. Держать QR без запаса бессмысленно: любая следующая правка его сломает, поэтому единственный источник — файл [`incy-routing-macos-link.txt`](incy-routing-macos-link.txt) и ссылка ниже.

> **v2** · sha256 `incy-routing-macos.json`: `ba8610e738da5010f200e8cd093b42aa6cf81f5bc8f1e61d11edc9848fdc36c6` — если хеш файла не совпадает, значит ссылка и QR собраны из другой версии профиля.

## ⚠️ Главное отличие от iPhone: режим подключения

На iPhone VPN один — другого варианта нет. На десктопе у INCY **два режима**, и от выбора зависит, сработает ли профиль вообще:

| Режим | Что попадает в туннель | Профиль маршрутизации |
|---|---|---|
| **TUN (VPN)** | Весь трафик системы: создаётся интерфейс `utun`, через Xray идут и TCP, и **UDP** | работает так же, как на iPhone — нужный режим для этого профиля |
| **Системный прокси** | Только программы, которые читают системные настройки прокси; **UDP не проксируется вообще** | работает частично: остальные программы ходят напрямую мимо правил, звонки и QUIC — мимо туннеля |

**Вывод: для этого профиля включай TUN.** В «Системном прокси» половина правил просто не сработает (приложение не спрашивает прокси), а голосовые вызовы не заработают никогда — даже с идеальными правилами.

> 🐞 **Оговорка к сборке 3.9.1 (ноябрь 2026):** в ней TUN поднимает интерфейс, но не открывает соединение с сервером — трафик уходит в туннель и умирает. Проверено на macOS: у процесса ни одной TCP-сессии к серверу, при этом тот же узел в xray-core 26.11.9 работает. Пока баг не исправлен, рабочий обходной путь — режим «Системный прокси» (браузеры работают, UDP/звонки — нет).

TUN на macOS работает через системное расширение: при первом включении macOS попросит разрешение (Системные настройки → Конфиденциальность и безопасность). Без него Connect будет падать без внятной ошибки.

## Чем этот профиль отличается от iPhone-версии

Профиль собран из того же ядра, что [iPhone-версия](https://github.com/IntruderHere/INCY-App-routing-on-iPhone) (v9): те же 34 сервиса, тот же полный список подсетей Telegram, тот же DNS. Отличия — только десктопные:

- **`Name` = «Socials via VPN · macOS»** — чтобы профили не путались между устройствами;
- **добавлен `geosite:github`** (64 домена: github.com, raw.githubusercontent.com, ghcr.io, codeload…) — на Mac это чаще нужно, чем на телефоне;
- **добавлено заблокированное в РФ**: целый реестр РКН одной строкой `geosite:blocked-in-ru` (~75 700 доменов) плюс восемь сервисов точечно — см. [раздел ниже](#что-добавлено-в-v2-заблокированное-в-рф);
- **`Geositeurl` задан** — геофайл берётся из обновляемого набора XTLS/Xray-rules-dat (7,8 МБ), потому что категории `blocked-in-ru` в предустановленном файле нет;
- **`UseChunkFiles` отсутствует** — на десктопе обрезка геофайлов не работает (документация INCY: `useChunkFiles` реализован только на Android/iOS), поэтому поле здесь бессмысленно;
- всё остальное — как на iPhone (те же 34 сервиса, те же подсети Telegram, тот же DNS).

> ⚠️ **Десктопный клиент в статусе pre-alpha** — это официальная формулировка INCY-DEV. Он пригоден для повседневной работы, но между релизами возможны ломающие изменения. Версия App Store (то же приложение, что на iPhone) от этого предупреждения свободна.

## Шаг 1. Установи INCY

Два пути, оба рабочие:

1. **App Store** — приложение INCY (LLC ITDEV), в списке платформ указаны iOS / Apple TV / **macOS**. Тот же код, что на iPhone, тот же профиль.
2. **DMG с GitHub** — сборка для Mac: Apple Silicon `incy-macos-arm64.dmg`, Intel `incy-macos-intel.dmg` (страница релизов [INCY-DEV/incy-platforms](https://github.com/INCY-DEV/incy-platforms/releases/latest)). Это и есть pre-alpha десктоп-клиент с режимами TUN / «Системный прокси».

## Шаг 2. Добавь подписку (сервер)

INCY серверы не предоставляет — нужна своя подписка (VLESS, VMess, Trojan, Hysteria2…).

1. Открой INCY → «**+**» → «Добавить подписку» (Import from URL) → вставь ссылку или отсканируй QR.
2. Разреши установку VPN-профиля / системного расширения, когда macOS спросит.
3. Выбери сервер и подключись — убедись, что туннель поднимается.

## Шаг 3. Добавь профиль маршрутизации (ключевой шаг)

### Вариант А — по ссылке (на Mac это основной способ)

1. Скопируй ссылку целиком.
2. Вставь её в адресную строку Safari и открой.
3. macOS предложит открыть INCY → подтверди. Профиль добавится и **сразу активируется**.

```
incy://routing/onadd/eyJOYW1lIjoiU29jaWFscyB2aWEgVlBOIMK3IG1hY09TIiwiR2xvYmFsUHJveHkiOiJmYWxzZSIsIlJlbW90ZUROU1R5cGUiOiJEb0giLCJSZW1vdGVETlNEb21haW4iOiJodHRwczovL2Nsb3VkZmxhcmUtZG5zLmNvbS9kbnMtcXVlcnkiLCJSZW1vdGVETlNJUCI6IjEuMS4xLjEiLCJEb21lc3RpY0ROU1R5cGUiOiJEb1UiLCJEb21lc3RpY0ROU0RvbWFpbiI6IiIsIkRvbWVzdGljRE5TSVAiOiI3Ny44OC44LjgiLCJEbnNIb3N0cyI6eyJjbG91ZGZsYXJlLWRucy5jb20iOiIxLjEuMS4xIn0sIkdlb3NpdGV1cmwiOiJodHRwczovL2dpdGh1Yi5jb20vWFRMUy9YcmF5LXJ1bGVzLWRhdC9yZWxlYXNlcy9sYXRlc3QvZG93bmxvYWQvZ2Vvc2l0ZS5kYXQiLCJGYWtlRE5TIjoiZmFsc2UiLCJEb21haW5TdHJhdGVneSI6IklQSWZOb25NYXRjaCIsIkxhc3RVcGRhdGVkIjoiMTc5NDY3MzgwMCIsIkRpcmVjdFNpdGVzIjpbImdlb3NpdGU6cHJpdmF0ZSJdLCJEaXJlY3RJcCI6WyJnZW9pcDpwcml2YXRlIiwiMTAuMC4wLjAvOCIsIjE3Mi4xNi4wLjAvMTIiLCIxOTIuMTY4LjAuMC8xNiIsIjE2OS4yNTQuMC4wLzE2IiwiMjI0LjAuMC4wLzQiLCIyNTUuMjU1LjI1NS4yNTUiXSwiUHJveHlTaXRlcyI6WyJnZW9zaXRlOnRlbGVncmFtIiwiZG9tYWluOnlvdXR1YmUuY29tIiwiZG9tYWluOnlvdXR1LmJlIiwiZG9tYWluOnl0LmJlIiwiZG9tYWluOmdvb2dsZXZpZGVvLmNvbSIsImRvbWFpbjp5dGltZy5jb20iLCJkb21haW46Z2dwaHQuY29tIiwiZG9tYWluOnlvdXR1YmUtbm9jb29raWUuY29tIiwiZG9tYWluOnlvdXR1YmVpLmdvb2dsZWFwaXMuY29tIiwiZG9tYWluOnlvdXR1YmUuZ29vZ2xlYXBpcy5jb20iLCJnZW9zaXRlOmluc3RhZ3JhbSIsImdlb3NpdGU6ZGlzY29yZCIsImdlb3NpdGU6d2hhdHNhcHAiLCJnZW9zaXRlOnR3aXR0ZXIiLCJnZW9zaXRlOmZhY2Vib29rIiwiZ2Vvc2l0ZTptZXNzZW5nZXIiLCJnZW9zaXRlOnNpZ25hbCIsImRvbWFpbjp0ZXh0c2VjdXJlLmNvbSIsImdlb3NpdGU6bGlua2VkaW4iLCJkb21haW46c25hcGNoYXQuY29tIiwiZG9tYWluOnNuYXAuY29tIiwiZG9tYWluOnNjLWNkbi5uZXQiLCJkb21haW46ZmFjZXRpbWUuYXBwbGUuY29tIiwiZG9tYWluOmZ0YXBpLml0dW5lcy5hcHBsZS5jb20iLCJkb21haW46cHVzaC5hcHBsZS5jb20iLCJkb21haW46ZXNzLmFwcGxlLmNvbSIsImdlb3NpdGU6dmliZXIiLCJnZW9zaXRlOm5ldGZsaXgiLCJnZW9zaXRlOnNwb3RpZnkiLCJnZW9zaXRlOnN0ZWFtIiwiZ2Vvc2l0ZTpvcGVuYWkiLCJkb21haW46Z2VtaW5pLmdvb2dsZS5jb20iLCJnZW9zaXRlOmFudGhyb3BpYyIsImRvbWFpbjptaWRqb3VybmV5LmNvbSIsImRvbWFpbjpkZWVwbC5jb20iLCJnZW9zaXRlOmNhbnZhIiwiZ2Vvc2l0ZTpub3Rpb24iLCJkb21haW46bWlyby5jb20iLCJnZW9zaXRlOnBsYXlzdGF0aW9uIiwiZ2Vvc2l0ZTpuaW50ZW5kbyIsImdlb3NpdGU6YmxpenphcmQiLCJnZW9zaXRlOmRpc25leSIsImdlb3NpdGU6Y291cnNlcmEiLCJnZW9zaXRlOmFpcmJuYiIsImdlb3NpdGU6Ym9va2luZyIsImRvbWFpbjpwYXJhbW91bnRwbHVzLmNvbSIsImRvbWFpbjpwYXJhbW91bnQuY29tIiwiZG9tYWluOnVuaXZlcnNhbHBsdXMuY29tIiwiZG9tYWluOnVuaXZlcnNhbC1wbHVzLmZyIiwiZ2Vvc2l0ZTp1Ymlzb2Z0IiwiZ2Vvc2l0ZTplYSIsImdlb3NpdGU6Z2l0aHViIiwiZ2Vvc2l0ZTpibG9ja2VkLWluLXJ1IiwiZ2Vvc2l0ZTpyb2Jsb3giLCJnZW9zaXRlOnJ1dHJhY2tlciIsImdlb3NpdGU6cHJvdG9ubWFpbCIsImdlb3NpdGU6c291bmRjbG91ZCIsImdlb3NpdGU6dGlrdG9rIiwiZ2Vvc2l0ZTpmaWdtYSIsImdlb3NpdGU6ZWJheSIsImdlb3NpdGU6c3BlZWR0ZXN0Il0sIlByb3h5SXAiOlsiOTEuMTA4LjQuMC8yMiIsIjkxLjEwOC44LjAvMjIiLCI5MS4xMDguMTIuMC8yMiIsIjkxLjEwOC4xNi4wLzIyIiwiOTEuMTA4LjIwLjAvMjIiLCI5MS4xMDguNTYuMC8yMiIsIjkxLjEwNS4xOTIuMC8yMyIsIjE0OS4xNTQuMTYwLjAvMjAiLCIxODUuNzYuMTUxLjAvMjQiLCI5NS4xNjEuNjQuMC8yMCIsIjIwMDE6NjdjOjRlODo6LzQ4IiwiMjAwMTpiMjg6ZjIzYzo6LzQ4IiwiMjAwMTpiMjg6ZjIzZDo6LzQ4IiwiMjAwMTpiMjg6ZjIzZjo6LzQ4IiwiMmEwYTpmMjgwOjovMzIiXSwiQmxvY2tTaXRlcyI6W10sIkJsb2NrSXAiOltdfQ==
```

### Вариант Б — через буфер обмена

Скопируй base64-часть после `incy://routing/onadd/` → в INCY добавь профиль маршрутизации из буфера.

## Шаг 4. Включи TUN и проверь

1. В настройках подключения выбери **TUN (VPN)**, а не «Системный прокси».
2. Подключись. Если macOS спросит про системное расширение — разреши и перезапусти INCY.
3. Проверь IP:
   - Safari → `2ip.ru` — должен быть твой **локальный** IP (правило direct);
   - Telegram Desktop → спроси у бота `@ip_bot` — должен быть IP **сервера VPN** (Telegram завёрнут в туннель).
4. Проверь сервисы: Telegram, Instagram (веб), YouTube, Discord, X, ChatGPT — работают; банк, Госуслуги, Яндекс — напрямую, как обычно.
5. Если включён **iCloud Private Relay** — выключи его на время проверки: он перехватывает трафик Safari и мешает понять, что происходит.

## Что входит в профиль (43 сервиса + реестр РКН)

| Категория | Сервисы |
|---|---|
| Мессенджеры | Telegram (+ все подсети в `ProxyIp`), WhatsApp, Viber, Signal (`geosite:signal` + `textsecure.com`) |
| Соцсети | Instagram, X/Twitter, Facebook + Messenger (`geosite:messenger` — `facebook` его не покрывает), LinkedIn, Snapchat |
| Видео и музыка | YouTube (явные домены + `youtubei.googleapis.com` — API приложения), Netflix, Spotify, Disney+ |
| Голос | Discord, FaceTime (сигналинг + APNs + IDS `ess.apple.com`) |
| Игры | Steam, PlayStation Network, Nintendo, Battle.net, Ubisoft, EA |
| AI и работа | OpenAI, Gemini, Anthropic, Midjourney, DeepL, Canva, Notion, Miro, Coursera |
| Путешествия и стриминг | Airbnb, Booking, Paramount+, Universal+ |
| Десктоп-дополнение | **GitHub** (`geosite:github`: github.com, raw.githubusercontent.com, ghcr.io, codeload…) |
| Заблокировано в РФ | **реестр РКН** (`geosite:blocked-in-ru`, ~75 700 доменов), Roblox, Rutracker, ProtonMail, SoundCloud, TikTok, Figma, eBay, Speedtest — см. [раздел ниже](#что-добавлено-в-v2-заблокированное-в-рф) |

Всё, что не совпало с правилами, идёт **напрямую** (`GlobalProxy: false`).

**IP-правила (`ProxyIp`) — только для Telegram:** полный официальный список подсетей ([core.telegram.org/resources/cidr.txt](https://core.telegram.org/resources/cidr.txt)) — 10 IPv4 и 5 IPv6. Telegram (и его звонки) адресуется по IP, а не по домену, поэтому без них не работает ни синхронизация, ни голос.

## Что добавлено в v2: заблокированное в РФ

Одна строка `geosite:blocked-in-ru` закрывает **весь реестр РКН** — 75 744 домена из [antifilter.download](https://antifilter.download) и [Re:filter](https://github.com/1andrevich/Re-filter-lists), включая Meta, X, Discord, Signal, Viber, Snapchat, LinkedIn, Roblox, Rutracker, TikTok, Speedtest, WhatsApp, YouTube, ProtonMail, SoundCloud и заблокированные медиа. Обновляется вместе с геофайлом — новый заблокированный домен попадает в маршрут без правки конфига.

Точечно добавлены сервисы, которых в реестре нет или которые нужны явно:

| Сервис | Категория | Почему |
|---|---|---|
| Roblox | `geosite:roblox` | заблокирован в РФ (2025) |
| Rutracker | `geosite:rutracker` | заблокирован с 2015 |
| ProtonMail | `geosite:protonmail` | `proton.me` в реестре |
| SoundCloud | `geosite:soundcloud` | в реестре |
| TikTok | `geosite:tiktok` | ограниченный режим с 2022 (загрузка, стримы, зарубежный контент) |
| Figma | `geosite:figma` | ушла из РФ, аккаунты ограничены |
| eBay | `geosite:ebay` | ушла из РФ |
| Speedtest | `geosite:speedtest` | `speedtest.net` в реестре |

**Зависимость.** Категории `blocked-in-ru` нет в предустановленном геофайле, поэтому в профиле задан `Geositeurl` на [XTLS/Xray-rules-dat](https://github.com/XTLS/Xray-rules-dat/releases/latest) — 7,8 МБ, скачивается один раз при добавлении профиля (дальше сверяется по хешу/`LastUpdated`). Если файл не скачался, правила из него просто не применяются, а остальные (Telegram, YouTube, GitHub и т.д.) продолжают работать из встроенного файла.

**Что не включено осознанно:**

- `geosite:cloudflare` — часть сайтов на Cloudflare в РФ действительно ломается, но это CDN: правило отправит через VPN и все российские сайты за Cloudflare. Если нужен — допиши одной строкой и проверь скорость;
- `geoip:blacklist-ru` — IP-список заблокированных сетей (нужен `Geoipurl` на `geoip-lite.dat`). Ловит то, что блокируют по IP, но заодно затягивает в туннель общие CDN-адреса (Google, Cloudflare), а они не заблокированы — в профиле с `GlobalProxy: false` это лишние потери.

## Telegram: почему не работают звонки

Звонок — отдельный транспорт: сервер Telegram отдаёт `PhoneConnection` с конкретными **IP:порт** (UDP), а при P2P телефон/клиент стучится напрямую на устройство собеседника. Домена у этого трафика нет — по `ProxySites` он не маршрутизируется никогда, только по `ProxyIp`.

Порядок проверки на Mac:

1. **Режим TUN.** В «Системном прокси» UDP не проксируется — звонки не заработают в принципе.
2. **«Блокировать UDP»** в настройках INCY — должен быть выключен.
3. **Узел.** Часть серверов режет UDP (антиабьюз, QUIC/Hysteria на сетях, где UDP шейпится). Работает на узле A и не работает на B — дело в узле.
4. **Telegram Desktop → Настройки → Продвинутые настройки → Peer-to-peer → «Никогда».** Иначе медиа идёт напрямую в сеть провайдера, где голос и режут.
5. **Второй собеседник в РФ тоже с VPN** — ограничение срабатывает с каждой стороны.
6. **Близкий узел.** Для голоса важнее пинг и потери: 40–70 мс до Европы лучше, чем 150 мс через океан; «робот» в трубке — это потери пакетов.

Проверить, профиль это или туннель: открой `incy://routing/off` (весь трафик в туннель). Идут звонки так, но не идут с профилем — правила; не идут и так — UDP/узел.

## Если что-то не работает

| Проблема | Решение |
|---|---|
| Connect есть, но сайты не открываются / правила не действуют | Включён «Системный прокси»: программы, которые его не спрашивают, идут мимо. Переключись на **TUN** |
| TUN не включается, Connect падает без ошибки | Не разрешено системное расширение: Системные настройки → Конфиденциальность и безопасность → Разрешить, затем перезапустить INCY |
| Connect в TUN есть, а через VPN ничего не открывается (`utun` поднят, но сайты висят) | Известный баг pre-alpha сборки 3.9.1: TUN поднимает интерфейс и дефолтный маршрут, но TCP-соединение к серверу не открывается вообще (в `lsof` у процесса нет ни одной сессии к серверу), при этом тот же узел в xray-core работает. Обходной путь — режим **«Системный прокси»**: браузеры и всё, что читает системный прокси, работает; UDP/звонки — нет. Баг отправлен в [feedback.incy.cc](https://feedback.incy.cc) |
| Звонки в Telegram молчат, чат работает | TUN + «Блокировать UDP» выкл. + P2P → «Никогда» + другой узел (см. раздел выше) |
| Заблокированный сервис открывается, а IP локальный | Профиль не активен или правило не совпало: проверь раздел «Маршрутизация» и что профиль именно «Socials via VPN · macOS» |
| У профиля красный «!» | Качаются геофайлы. На десктопе они **полные** и не обрезаются: `geosite.dat` из профиля — 7,8 МБ. Подожди минуту, это нормально |
| Заблокированное не открывается, остальное работает | Проверь, что профиль активен и что геофайл скачался (раздел «Маршрутизация»): без него `blocked-in-ru` не применяется |
| После перезагрузки Mac ничего не работает | Проверь, что INCY запущен и TUN поднялся; при сомнениях — «Сбросить Kill Switch» в настройках |
| Хочешь «всё через VPN» | Открой `incy://routing/off` (профиль сохранится) |
| Хочешь «всё напрямую» без VPN | Просто отключи подключение в INCY |

## JSON профиля (если захочешь отредактировать)

```json
{
 "Name": "Socials via VPN · macOS",
 "GlobalProxy": "false",
 "RemoteDNSType": "DoH",
 "RemoteDNSDomain": "https://cloudflare-dns.com/dns-query",
 "RemoteDNSIP": "1.1.1.1",
 "DomesticDNSType": "DoU",
 "DomesticDNSDomain": "",
 "DomesticDNSIP": "77.88.8.8",
 "DnsHosts": {
  "cloudflare-dns.com": "1.1.1.1"
 },
 "Geositeurl": "https://github.com/XTLS/Xray-rules-dat/releases/latest/download/geosite.dat",
 "FakeDNS": "false",
 "DomainStrategy": "IPIfNonMatch",
 "LastUpdated": "1794673800",
 "DirectSites": [
  "geosite:private"
 ],
 "DirectIp": [
  "geoip:private",
  "10.0.0.0/8",
  "172.16.0.0/12",
  "192.168.0.0/16",
  "169.254.0.0/16",
  "224.0.0.0/4",
  "255.255.255.255"
 ],
 "ProxySites": [
  "geosite:telegram",
  "domain:youtube.com",
  "domain:youtu.be",
  "domain:yt.be",
  "domain:googlevideo.com",
  "domain:ytimg.com",
  "domain:ggpht.com",
  "domain:youtube-nocookie.com",
  "domain:youtubei.googleapis.com",
  "domain:youtube.googleapis.com",
  "geosite:instagram",
  "geosite:discord",
  "geosite:whatsapp",
  "geosite:twitter",
  "geosite:facebook",
  "geosite:messenger",
  "geosite:signal",
  "domain:textsecure.com",
  "geosite:linkedin",
  "domain:snapchat.com",
  "domain:snap.com",
  "domain:sc-cdn.net",
  "domain:facetime.apple.com",
  "domain:ftapi.itunes.apple.com",
  "domain:push.apple.com",
  "domain:ess.apple.com",
  "geosite:viber",
  "geosite:netflix",
  "geosite:spotify",
  "geosite:steam",
  "geosite:openai",
  "domain:gemini.google.com",
  "geosite:anthropic",
  "domain:midjourney.com",
  "domain:deepl.com",
  "geosite:canva",
  "geosite:notion",
  "domain:miro.com",
  "geosite:playstation",
  "geosite:nintendo",
  "geosite:blizzard",
  "geosite:disney",
  "geosite:coursera",
  "geosite:airbnb",
  "geosite:booking",
  "domain:paramountplus.com",
  "domain:paramount.com",
  "domain:universalplus.com",
  "domain:universal-plus.fr",
  "geosite:ubisoft",
  "geosite:ea",
  "geosite:github",
  "geosite:blocked-in-ru",
  "geosite:roblox",
  "geosite:rutracker",
  "geosite:protonmail",
  "geosite:soundcloud",
  "geosite:tiktok",
  "geosite:figma",
  "geosite:ebay",
  "geosite:speedtest"
 ],
 "ProxyIp": [
  "91.108.4.0/22",
  "91.108.8.0/22",
  "91.108.12.0/22",
  "91.108.16.0/22",
  "91.108.20.0/22",
  "91.108.56.0/22",
  "91.105.192.0/23",
  "149.154.160.0/20",
  "185.76.151.0/24",
  "95.161.64.0/20",
  "2001:67c:4e8::/48",
  "2001:b28:f23c::/48",
  "2001:b28:f23d::/48",
  "2001:b28:f23f::/48",
  "2a0a:f280::/32"
 ],
 "BlockSites": [],
 "BlockIp": []
}
```

Пересобрать ссылку после правки: закодировать JSON в base64 без пробелов и открыть `incy://routing/onadd/{base64}`. Профили с одинаковым `Name` обновляются, а не дублируются; `LastUpdated` (unix-время) решает, какая версия свежее.

## Источники

- Документация INCY (поля профиля, DNS, ссылки): [docs.incy.cc/routing](https://incy.gitbook.io/docs/docs-en/routing)
- Платформы и режимы, per-app только Android/desktop, pre-alpha десктопа: [INCY-DEV/incy-platforms](https://github.com/INCY-DEV/incy-platforms)
- Официальный список подсетей Telegram: [core.telegram.org/resources/cidr.txt](https://core.telegram.org/resources/cidr.txt)
- Как устроены звонки (`PhoneConnection`, `ip`/`port`, `udp_p2p`): [core.telegram.org/api/calls](https://core.telegram.org/api/calls)
- Списки доменов: [v2fly/domain-list-community](https://github.com/v2fly/domain-list-community)
- iPhone-версия профиля и разбор по сервисам: [IntruderHere/INCY-App-routing-on-iPhone](https://github.com/IntruderHere/INCY-App-routing-on-iPhone)
