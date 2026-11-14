# INCY на macOS: 35 сервисов через VPN, всё остальное — без

> 🇬🇧 **English version:** [README-EN.md](README-EN.md)

## ⚖️ Правовая оговорка (прочитай сначала)

Материал носит **информационно-технический характер**: это личный гайд по настройке клиента INCY (профиль маршрутизации) на macOS. Он **не является рекламой и не продвигает** ни один VPN-сервис, не содержит призывов к его использованию и не предоставляет доступа к сторонним сервисам.

Автору известно, что законодательство РФ регулирует распространение информации о средствах обхода блокирования:
- **8-ФЗ «Об информации, информационных технологиях и о защите информации»** (ст. 15) — блокировка страниц с «информацией о способах, методах обеспечения доступа к информационным ресурсам, доступ к которым ограничен на территории РФ»;
- **Приказ Роскомнадзора от 17.10.2024 № 196** — критерии запрещённой информации, включая научно-технические данные о VPN-сервисах, используемых для обхода блокировок;
- **281-ФЗ от 31.07.2025** — поправки в КоАП РФ: реклама VPN-сервисов (ст. 14.2) и умышленный поиск экстремистских материалов (ст. 13.53);
- **282-ФЗ от 31.07.2025** — использование средств обхода блокирования при совершении преступления является отягчающим обстоятельством (ст. 63 УК РФ).

Автор **не призывает к обходу блокировок**, установленных в соответствии с законодательством РФ. Использование материала — на усмотрение и риск читателя, в соответствии с действующим законодательством РФ. По состоянию на дату публикации сам факт использования VPN физическим лицом административной ответственностью в КоАП РФ не предусмотрен. Текст не является юридической консультацией; за актуальным мнением обращайтесь к юристу.

<p align="center">
  <img src="incy-routing-macos-qr.png" width="360" alt="QR — импорт профиля маршрутизации «Socials via VPN · macOS»">
</p>

**На Mac профиль ставится ссылкой** (она в [Шаге 3](#шаг-3-добавь-профиль-маршрутизации-ключевой-шаг)): открой её в Safari — macOS предложит открыть INCY. QR — на случай, если импортировать нужно с другого устройства.

> **v1** · sha256 `incy-routing-macos.json`: `63b1b666147399081442d2d341ecf460771a944f1ca97e0da1a84e50a47e7a0d` — если хеш файла не совпадает, значит ссылка и QR собраны из другой версии профиля.

## ⚠️ Главное отличие от iPhone: режим подключения

На iPhone VPN один — другого варианта нет. На десктопе у INCY **два режима**, и от выбора зависит, сработает ли профиль вообще:

| Режим | Что попадает в туннель | Профиль маршрутизации |
|---|---|---|
| **TUN (VPN)** | Весь трафик системы: создаётся интерфейс `utun`, через Xray идут и TCP, и **UDP** | работает так же, как на iPhone — нужный режим для этого профиля |
| **Системный прокси** | Только программы, которые читают системные настройки прокси; **UDP не проксируется вообще** | работает частично: остальные программы ходят напрямую мимо правил, звонки и QUIC — мимо туннеля |

**Вывод: для этого профиля включай TUN.** В «Системном прокси» половина правил просто не сработает (приложение не спрашивает прокси), а голосовые вызовы не заработают никогда — даже с идеальными правилами.

TUN на macOS работает через системное расширение: при первом включении macOS попросит разрешение (Системные настройки → Конфиденциальность и безопасность). Без него Connect будет падать без внятной ошибки.

## Чем этот профиль отличается от iPhone-версии

Профиль собран из того же ядра, что [iPhone-версия](https://github.com/IntruderHere/INCY-App-routing-on-iPhone) (v9): те же 34 сервиса, тот же полный список подсетей Telegram, тот же DNS. Отличия — только десктопные:

- **`Name` = «Socials via VPN · macOS»** — чтобы профили не путались между устройствами;
- **`geosite:github`** добавлен (64 домена: github.com, raw.githubusercontent.com, ghcr.io, codeload…) — на Mac это чаще нужно, чем на телефоне. Удаляется одной строкой;
- **`UseChunkFiles` отсутствует** — на десктопе обрезка геофайлов не работает (документация INCY: `useChunkFiles` реализован только на Android/iOS), поэтому поле здесь бессмысленно;
- всё остальное — байт в байт как на iPhone.

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
incy://routing/onadd/eyJOYW1lIjoiU29jaWFscyB2aWEgVlBOIMK3IG1hY09TIiwiR2xvYmFsUHJveHkiOiJmYWxzZSIsIlJlbW90ZUROU1R5cGUiOiJEb0giLCJSZW1vdGVETlNEb21haW4iOiJodHRwczovL2Nsb3VkZmxhcmUtZG5zLmNvbS9kbnMtcXVlcnkiLCJSZW1vdGVETlNJUCI6IjEuMS4xLjEiLCJEb21lc3RpY0ROU1R5cGUiOiJEb1UiLCJEb21lc3RpY0ROU0RvbWFpbiI6IiIsIkRvbWVzdGljRE5TSVAiOiI3Ny44OC44LjgiLCJEbnNIb3N0cyI6eyJjbG91ZGZsYXJlLWRucy5jb20iOiIxLjEuMS4xIn0sIkZha2VETlMiOiJmYWxzZSIsIkRvbWFpblN0cmF0ZWd5IjoiSVBJZk5vbk1hdGNoIiwiTGFzdFVwZGF0ZWQiOiIxNzk0NjcyMDAwIiwiRGlyZWN0U2l0ZXMiOlsiZ2Vvc2l0ZTpwcml2YXRlIl0sIkRpcmVjdElwIjpbImdlb2lwOnByaXZhdGUiLCIxMC4wLjAuMC84IiwiMTcyLjE2LjAuMC8xMiIsIjE5Mi4xNjguMC4wLzE2IiwiMTY5LjI1NC4wLjAvMTYiLCIyMjQuMC4wLjAvNCIsIjI1NS4yNTUuMjU1LjI1NSJdLCJQcm94eVNpdGVzIjpbImdlb3NpdGU6dGVsZWdyYW0iLCJkb21haW46eW91dHViZS5jb20iLCJkb21haW46eW91dHUuYmUiLCJkb21haW46eXQuYmUiLCJkb21haW46Z29vZ2xldmlkZW8uY29tIiwiZG9tYWluOnl0aW1nLmNvbSIsImRvbWFpbjpnZ3BodC5jb20iLCJkb21haW46eW91dHViZS1ub2Nvb2tpZS5jb20iLCJkb21haW46eW91dHViZWkuZ29vZ2xlYXBpcy5jb20iLCJkb21haW46eW91dHViZS5nb29nbGVhcGlzLmNvbSIsImdlb3NpdGU6aW5zdGFncmFtIiwiZ2Vvc2l0ZTpkaXNjb3JkIiwiZ2Vvc2l0ZTp3aGF0c2FwcCIsImdlb3NpdGU6dHdpdHRlciIsImdlb3NpdGU6ZmFjZWJvb2siLCJnZW9zaXRlOm1lc3NlbmdlciIsImdlb3NpdGU6c2lnbmFsIiwiZG9tYWluOnRleHRzZWN1cmUuY29tIiwiZ2Vvc2l0ZTpsaW5rZWRpbiIsImRvbWFpbjpzbmFwY2hhdC5jb20iLCJkb21haW46c25hcC5jb20iLCJkb21haW46c2MtY2RuLm5ldCIsImRvbWFpbjpmYWNldGltZS5hcHBsZS5jb20iLCJkb21haW46ZnRhcGkuaXR1bmVzLmFwcGxlLmNvbSIsImRvbWFpbjpwdXNoLmFwcGxlLmNvbSIsImRvbWFpbjplc3MuYXBwbGUuY29tIiwiZ2Vvc2l0ZTp2aWJlciIsImdlb3NpdGU6bmV0ZmxpeCIsImdlb3NpdGU6c3BvdGlmeSIsImdlb3NpdGU6c3RlYW0iLCJnZW9zaXRlOm9wZW5haSIsImRvbWFpbjpnZW1pbmkuZ29vZ2xlLmNvbSIsImdlb3NpdGU6YW50aHJvcGljIiwiZG9tYWluOm1pZGpvdXJuZXkuY29tIiwiZG9tYWluOmRlZXBsLmNvbSIsImdlb3NpdGU6Y2FudmEiLCJnZW9zaXRlOm5vdGlvbiIsImRvbWFpbjptaXJvLmNvbSIsImdlb3NpdGU6cGxheXN0YXRpb24iLCJnZW9zaXRlOm5pbnRlbmRvIiwiZ2Vvc2l0ZTpibGl6emFyZCIsImdlb3NpdGU6ZGlzbmV5IiwiZ2Vvc2l0ZTpjb3Vyc2VyYSIsImdlb3NpdGU6YWlyYm5iIiwiZ2Vvc2l0ZTpib29raW5nIiwiZG9tYWluOnBhcmFtb3VudHBsdXMuY29tIiwiZG9tYWluOnBhcmFtb3VudC5jb20iLCJkb21haW46dW5pdmVyc2FscGx1cy5jb20iLCJkb21haW46dW5pdmVyc2FsLXBsdXMuZnIiLCJnZW9zaXRlOnViaXNvZnQiLCJnZW9zaXRlOmVhIiwiZ2Vvc2l0ZTpnaXRodWIiXSwiUHJveHlJcCI6WyI5MS4xMDguNC4wLzIyIiwiOTEuMTA4LjguMC8yMiIsIjkxLjEwOC4xMi4wLzIyIiwiOTEuMTA4LjE2LjAvMjIiLCI5MS4xMDguMjAuMC8yMiIsIjkxLjEwOC41Ni4wLzIyIiwiOTEuMTA1LjE5Mi4wLzIzIiwiMTQ5LjE1NC4xNjAuMC8yMCIsIjE4NS43Ni4xNTEuMC8yNCIsIjk1LjE2MS42NC4wLzIwIiwiMjAwMTo2N2M6NGU4OjovNDgiLCIyMDAxOmIyODpmMjNjOjovNDgiLCIyMDAxOmIyODpmMjNkOjovNDgiLCIyMDAxOmIyODpmMjNmOjovNDgiLCIyYTBhOmYyODA6Oi8zMiJdLCJCbG9ja1NpdGVzIjpbXSwiQmxvY2tJcCI6W119
```

### Вариант Б — по QR-коду

QR — в начале этой инструкции (или файл `incy-routing-macos-qr.png`): INCY → сканирование QR → наведи. Удобно, когда профиль ставится не на тот Mac, где открыт этот файл.

### Вариант В — через буфер обмена

Скопируй base64-часть после `incy://routing/onadd/` → в INCY добавь профиль маршрутизации из буфера.

## Шаг 4. Включи TUN и проверь

1. В настройках подключения выбери **TUN (VPN)**, а не «Системный прокси».
2. Подключись. Если macOS спросит про системное расширение — разреши и перезапусти INCY.
3. Проверь IP:
   - Safari → `2ip.ru` — должен быть твой **локальный** IP (правило direct);
   - Telegram Desktop → спроси у бота `@ip_bot` — должен быть IP **сервера VPN** (Telegram завёрнут в туннель).
4. Проверь сервисы: Telegram, Instagram (веб), YouTube, Discord, X, ChatGPT — работают; банк, Госуслуги, Яндекс — напрямую, как обычно.
5. Если включён **iCloud Private Relay** — выключи его на время проверки: он перехватывает трафик Safari и мешает понять, что происходит.

## Что входит в профиль (35 сервисов)

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

Всё, что не совпало с правилами, идёт **напрямую** (`GlobalProxy: false`).

**IP-правила (`ProxyIp`) — только для Telegram:** полный официальный список подсетей ([core.telegram.org/resources/cidr.txt](https://core.telegram.org/resources/cidr.txt)) — 10 IPv4 и 5 IPv6. Telegram (и его звонки) адресуется по IP, а не по домену, поэтому без них не работает ни синхронизация, ни голос.

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
| Звонки в Telegram молчат, чат работает | TUN + «Блокировать UDP» выкл. + P2P → «Никогда» + другой узел (см. раздел выше) |
| Заблокированный сервис открывается, а IP локальный | Профиль не активен или правило не совпало: проверь раздел «Маршрутизация» и что профиль именно «Socials via VPN · macOS» |
| У профиля красный «!» | Качаются геофайлы. На десктопе они **полные** (десятки МБ) и не обрезаются — подожди, это нормально |
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
 "FakeDNS": "false",
 "DomainStrategy": "IPIfNonMatch",
 "LastUpdated": "1794672000",
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
  "geosite:github"
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
