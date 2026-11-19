# FX Terminal

Конвертер валют в стиле биржевого терминала: BYN, RUB, EUR, USD, PLN.
Один HTML-файл в `www/`, упаковка в Android-приложение через Capacitor.

## Возможности

- Загрузка курсов из сети с двумя источниками и таймаутом 7 с
- Кэш в `localStorage`, оффлайн-режим и встроенные резервные курсы
- Колонка Δ — изменение курса относительно предыдущей загрузки
- Переключаемые скины внутри приложения, выбор сохраняется
- Строго квадратные кнопки (`aspect-ratio: 1/1`), вёрстка на `100dvh` без прокрутки
- Ввод с экранной и с физической клавиатуры

## Скины

Ряд чипов под заголовком переключает тему, выбор пишется в `localStorage` (`fx.skin`)
и восстанавливается при следующем запуске.

| Скин | Описание |
|---|---|
| `terminal` | тёмно-зелёный, моноширинный, нулевой радиус |
| `fintech` | #0B0E14, акцент #1FD6A0, радиус 10px |
| `light` | #F7F8FA, белые карточки, акцент #2563EB |
| `glass` | градиент синий→фиолетовый, `backdrop-filter: blur(16px)` |
| `brutal` | кремовый фон, рамки 3px, жёсткие тени без размытия |

Темы — наборы CSS-переменных на `body[data-skin=...]` (`--bg`, `--pan`, `--grid`, `--acc`,
`--rad`, `--bw`, `--sh`, `--fnt`, `--num`). Разметка и логика одни и те же, новый скин
добавляется одним блоком в CSS и одной строкой в массиве `SKINS`.
При переключении обновляется `meta[name=theme-color]`, поэтому в Android-WebView
системная строка подхватывает цвет темы.

## Запуск в браузере

Открыть `www/index.html`. Внешних зависимостей нет.

## Сборка APK локально

Требуется Node 20, JDK 17, Android SDK (platform 34 и build-tools).

```bash
npm install
npx cap add android     # один раз; папка android/ в .gitignore
npx cap sync android
cd android && ./gradlew assembleDebug
```

APK: `android/app/build/outputs/apk/debug/app-debug.apk`

После любого изменения в `www/` — снова `npx cap sync android`.

## Сборка APK в GitHub Actions

Файл workflow лежит в `ci/android.yml`. Чтобы сборка заработала, переместите его:

```bash
git clone https://github.com/anderza/fx-terminal
cd fx-terminal
mkdir -p .github/workflows
git mv ci/android.yml .github/workflows/android.yml
git commit -am "ci: enable Android build"
git push
```

Дальше каждый push в `main` собирает debug-APK и кладёт его в артефакты запуска:
Actions -> запуск -> Artifacts -> `fx-terminal-debug-apk`.

Сам положить файл в `.github/workflows/` не смог: у токена интеграции нет права `workflow`.

## Релизная подпись

Для Google Play нужен keystore и подписанный AAB. Ключи держите в GitHub Secrets
(`KEYSTORE_BASE64`, `KEYSTORE_PASSWORD`, `KEY_ALIAS`, `KEY_PASSWORD`), в репозиторий их не коммитить.

## Windows

Тот же `www/` оборачивается в Tauri или Electron. В этот репозиторий ещё не добавлено.

## Источники курсов

- `open.er-api.com` — без ключа
- `@fawazahmed0/currency-api` через jsDelivr — резерв
