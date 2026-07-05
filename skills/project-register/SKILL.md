---
name: project-register
description: Подключить репозиторий в систему хаба — identity-блок, симлинк роли, строка в HQ.md. Use when connecting an existing repo to the hub, creating a new project repo, user says "project-register", "подключи репо", "зарегистрируй проект", "--scan". Also used by /kickoff with --new to create a project repo.
disable-model-invocation: true
---

# Project Register

Регистрирует репозиторий в системе хаба. Три режима: один существующий репо (default),
`--scan` (пачкой), `--new <name>` (создать с нуля — так его вызывает `/kickoff`).

## Конфиг

Найди штаб: репо с `## Hub Config` в AGENTS.md (или CLAUDE.md) (обычно `<repos_root>/<owner>-hub`; если
не находится — спроси путь). Прочитай из Hub Config: `owner`, `hub_repo`, `hub_path`,
`repos_root`, `role_label_prefix`. Роли — файлы `departments/*.md`. Реестр — `HQ.md`.

## Преflight (read-only, всегда первым)

Для каждого целевого репо доложи одной строкой:
`локально: есть/нет · GitHub: есть/нет · в HQ.md: есть/нет · AGENTS.md|CLAUDE.md: есть/есть с identity/нет · предлагаемая роль: <slug>`

Роль предложи по содержимому (язык, README, имя), но **спроси подтверждение** — не угадывай молча.

## Действия (после подтверждения)

1. **Локальный клон в корне**: нет локально → `gh repo clone` в `<repos_root>/<name>`;
   лежит в другом месте → предложи перенести `mv` в корень (сначала проверь, что на
   старый путь не завязаны сервисы: `grep -r <старый-путь> ~/.config/systemd crontab -l`);
   `--new` → `mkdir` + `git init -b main` + README + `gh repo create --private --source . --push`.
2. **Identity-блок**: вставь `template/project-AGENTS.md.tmpl` (из
   `${CLAUDE_PLUGIN_ROOT}/template/`; переменной нет — клон hub-kit) **В НАЧАЛО**
   канонического файла проекта. Канонизация agent-agnostic:
   - есть только `CLAUDE.md` (не симлинк) → `git mv CLAUDE.md AGENTS.md`;
   - нет ни одного → создай `AGENTS.md` из шаблона;
   - затем симлинк-двойник: `ln -sfn AGENTS.md CLAUDE.md`.
   Существующее содержимое сохрани ниже блока — оно становится local-правилами.
   Уже есть блок (`hub-kit identity block`) — не дублируй.
3. **Симлинк роли** (относительный!):
   `ln -sfn ../<имя-штаба>/departments/<role>.md <repo>/.dept.md`
   Проверь, что резолвится: `test -e <repo>/.dept.md`. Не резолвится (репо не в корне) —
   это ошибка шага 1, не ставь абсолютный путь.
4. **HQ.md**: добавь строку `| <role> | <owner>/<name> | <path> | <домен одной строкой> |`.
5. **Routing**: предложи 3–6 ключевых слов для этого репо в `routing:` Hub Config.
6. **Лейблы в репо проекта**: `epic`, `backlog`, `dept:<role>` (создай отсутствующие).
7. **Коммиты**: в репо проекта (`chore: register in <hub> [role: <slug>]` — AGENTS.md +
   симлинки CLAUDE.md/.dept.md) и в штабе (`chore: register <name>` — HQ.md + AGENTS.md).
   Пушь штаб; пуш проекта — спроси, если у него настроен авто-деплой с ветки.

## --scan

`ls <repos_root>` + `gh repo list <owner> --limit 100` → таблица кандидатов
(`репо | локально | активность | предлагаемая роль`), пользователь отмечает нужные →
прогони каждый по шагам выше. Не предлагай форки и archived.

## Red flags — STOP

| Собираешься… | Вместо этого… |
|---|---|
| Затереть существующий CLAUDE.md/AGENTS.md проекта | Identity-блок СВЕРХУ, остальное сохранить |
| Поставить абсолютный симлинк | Перенести репо в корень, симлинк относительный |
| Угадать роль молча | Предложить и спросить |
| Push в репо с авто-деплоем | Спросить (push = redeploy) |
