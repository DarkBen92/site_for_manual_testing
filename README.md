# site_for_manual_testing

Учебный репозиторий для практики ручного тестирования и работы с Git.

Этот README описывает подключение к GitHub по SSH, скачивание репозитория и отправку изменений через `git push`.

## Что понадобится

- Git и SSH-клиент на компьютере.
- Аккаунт GitHub. Для отправки изменений нужны права записи в репозиторий.
- Командная строка: **cmd в Windows** или **Терминал в macOS/Linux**.

Проверь установку:

```text
git --version
ssh -V
```

Обе команды должны вывести версии программ. Если команда не найдена, установи Git или SSH-клиент и открой консоль заново.

## 1. Создать SSH-ключ

SSH использует пару ключей: публичный добавляется в GitHub, приватный остаётся на компьютере.

```text
ssh-keygen -t ed25519 -C "your_email@example.com"
```

Замени почту на свою: она используется как подпись ключа.

1. Нажми Enter, чтобы сохранить ключ в стандартную папку `.ssh` внутри домашней папки пользователя.
2. Если программа предлагает перезаписать существующий ключ, откажись. Существующий ключ можно использовать повторно.
3. Задай парольную фразу для защиты ключа и повтори её. При вводе символы не отображаются.

Если использовано стандартное имя, появятся два файла:

- `id_ed25519` — приватный ключ. Не публикуй его и не добавляй в репозиторий.
- `id_ed25519.pub` — публичный ключ. Его содержимое нужно добавить в GitHub.

## 2. Добавить публичный ключ в GitHub

Выведи содержимое публичного ключа.

**Windows cmd:**

```cmd
type "%USERPROFILE%\.ssh\id_ed25519.pub"
```

**macOS/Linux:**

```sh
cat ~/.ssh/id_ed25519.pub
```

Если используешь ключ с другим именем, подставь путь к его файлу `.pub`.

Скопируй всю строку, начинающуюся с `ssh-ed25519`.

В GitHub открой **аватар → Settings → SSH and GPG keys → New SSH key**:

1. В **Title** укажи понятное название, например `Мой ноутбук`.
2. Выбери тип **Authentication Key**.
3. Вставь публичный ключ в поле **Key** и сохрани.

## 3. Проверить подключение

```text
ssh -T git@github.com
```

`git` — служебное имя пользователя. Не заменяй его своим логином GitHub.

При первом подключении сравни отпечаток сервера с [официальными отпечатками GitHub](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/githubs-ssh-key-fingerprints). Если он совпадает, введи `yes`. При запросе парольной фразы введи пароль от SSH-ключа.

Успешный ответ выглядит так:

```text
Hi USERNAME! You've successfully authenticated, but GitHub does not provide shell access.
```

Сообщение об отсутствии shell-доступа нормально: для Git он не требуется. Проверка `ssh -T` у GitHub может завершиться с кодом `1` даже при успешной аутентификации — ориентируйся на текст ответа.

## 4. Скачать репозиторий по SSH

Перейди в папку, где хочешь хранить проект, и выполни:

```text
git clone git@github.com:DarkBen92/site_for_manual_testing.git
cd site_for_manual_testing
```

`git clone` скачивает проект и его историю в новую папку.

Если репозиторий уже скачан, повторное клонирование не нужно. Открой существующую папку проекта и переключи адрес `origin` на SSH:

```text
git remote set-url origin git@github.com:DarkBen92/site_for_manual_testing.git
git remote -v
```

В выводе для `fetch` и `push` должен быть SSH-адрес. `origin` — имя удалённого репозитория в локальных настройках Git.

## 5. Отправить изменения через git push

Выполняй команды из папки проекта. Пример ниже отправляет изменение `README.md` в ветку `main`.

Проверь текущую ветку и изменения:

```text
git branch --show-current
git status
git diff -- README.md
```

Убедись, что находишься в `main`. Для нового, ещё не отслеживаемого файла содержимое проверяют в редакторе: обычный `git diff` его не показывает.

Если Git при первом коммите просит представиться, укажи свои данные только для этого проекта:

```text
git config user.name "Твоё имя"
git config user.email "your_email@example.com"
```

Подготовь файл, проверь изменения и создай коммит:

```text
git add README.md
git diff --cached -- README.md
git commit -m "docs: update README"
```

- `git add` добавляет выбранный файл в подготовленные изменения.
- `git diff --cached` показывает, что попадёт в коммит.
- `git commit` сохраняет изменения в локальной истории.

Для других файлов указывай их пути вместо `README.md`.

Отправь коммит на GitHub:

```text
git push -u origin main
```

`-u` связывает локальную ветку `main` с `origin/main`. После успешной первой отправки обычно достаточно:

```text
git push
```

Проверь результат:

```text
git status
```

Открой репозиторий на GitHub и убедись, что появился твой коммит. Локальный коммит становится доступен другим после успешного `push`.

## Частые ошибки

- **`Permission denied (publickey)`** — проверь, что публичный ключ добавлен в нужный аккаунт, а приватный ключ доступен SSH-клиенту. Повтори `ssh -T git@github.com`.
- **`Repository not found`** — проверь адрес `origin` и доступ аккаунта к репозиторию. Успешная SSH-аутентификация сама по себе не даёт прав на любой проект.
- **`src refspec main does not match any`** — проверь, что создан хотя бы один коммит и текущая ветка называется `main`.
- **`non-fast-forward` / `fetch first`** — в удалённой ветке есть коммиты, которых нет локально. Для общей истории выполни `git pull --rebase origin main`, разреши конфликты при их наличии и повтори `git push`. Не используй `--force`, чтобы просто обойти ошибку.

## Официальные инструкции

- [Создание SSH-ключа и настройка ssh-agent](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent).
- [Добавление публичного ключа в GitHub](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account).
- [Проверка SSH-подключения](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection).
- [Репозиторий проекта](https://github.com/DarkBen92/site_for_manual_testing).
