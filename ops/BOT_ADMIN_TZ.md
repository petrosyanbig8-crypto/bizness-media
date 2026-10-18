# ТЗ: админ-панель владельца в @biznes_na_ii_bot

Цель: владелец пишет Claude и смотрит статус контента прямо в боте.
Claude не держит бота: бот остаётся на вашем сервере, Claude читает и пишет файлы в этом репозитории.

## 1. Доступ
- `OWNER_ID` — Telegram user id владельца (переменная окружения).
- Все команды ниже работают только для `OWNER_ID`. Остальным — обычное меню бота.
- При первом /start владельца записать его chat_id в `ops/owner.json`: `{"chat_id": 123}`.

## 2. Меню (reply-клавиатура, только владельцу)
`✍️ Написать Claude` · `📋 Задачи` · `🎬 Ролики` · `📝 Посты`

## 3. Кнопки
- **📋 Задачи / 🎬 Ролики / 📝 Посты** — бот читает
  `https://raw.githubusercontent.com/petrosyanbig8-crypto/bizness-media/main/ops/status.json`
  (поля `tasks`, `videos`, `posts`) и выводит списком. Кэш не больше 5 минут.
- **✍️ Написать Claude** — бот просит текст/голосовое/файл и добавляет запись в `ops/inbox.json`
  через GitHub API (`PUT /repos/petrosyanbig8-crypto/bizness-media/contents/ops/inbox.json`):
  `{"id": <message_id>, "ts": "<ISO>", "type": "text|voice|file", "text": "...", "file_id": "...", "done": false}`.
  Ответ владельцу: «Передал Claude. Ответ придёт в течение часа».
  Голосовые и файлы: сохранять `file_id`, сам файл можно положить в `inbox/` репозитория.

## 4. Ответы Claude
Claude раз в час читает `ops/inbox.json`, отвечает владельцу сообщением от бота
(через Bot API, chat_id из `ops/owner.json`) и ставит записи `"done": true`.
Боту ничего делать не нужно, главное — не перезаписывать поле `done`.

## 5. Токен GitHub
Fine-grained token только на репозиторий `bizness-media`, права Contents: Read and write.

## 6. Важно
- Публикации в соцсети бот не запускает: их подтверждает владелец в чате Claude.
- Если бот работает через polling — ничего менять не нужно, Claude не читает getUpdates.
