# Durable Work

Эта структура готова к первому запуску Producer.

## `outcomes/`

Один файл на активный или исторически значимый Player Outcome. Создавать из `templates/outcome.md`.

Lifecycle:

`proposed → active → accepting → accepted | rejected`

Outcome закрывается только после integrated evidence и нужного Human/playtest verdict.

## `open/`

Один файл на Atomic Card. Создавать из `templates/task.md`.

Card существует ради Parent Outcome. После integration и acceptance она удаляется из `open/`; durable результат остаётся в Outcome, Git history и authoritative feature truth.

Batch не хранится здесь как отдельный backlog level. Producer временно собирает связанные Cards в Orca Task по context locality и ownership.
