# AI Code Review — GitHub Action

RAG-ассистированное AI-ревью pull request'ов. Экшен берёт diff PR'а и
отправляет его в ваш инстанс сервиса [llm-code-review](https://github.com/TuychiyevNodirbek/llm-code-review)
(FastAPI + LLM-роутер Groq/Gemini/OpenRouter с фолбэком + RAG-поиск контекста
по кодовой базе через Postgres/pgvector). Ответ с находками постится
комментарием в PR.

## Быстрый старт

1. Задеплойте сервис [llm-code-review](https://github.com/TuychiyevNodirbek/llm-code-review) — получите публичный `API_URL`.
2. В репозитории, который хотите ревьюить, добавьте secrets:
   - `REVIEW_API_URL` — URL вашего сервиса
   - `REVIEW_API_TOKEN` — токен аутентификации (`REVIEW_API_TOKEN` из `.env` сервиса)
3. Добавьте workflow, например `.github/workflows/ai-review.yml`:

```yaml
name: AI Code Review

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
      contents: read
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: TuychiyevNodirbek/ai-code-review-action@v1
        with:
          api-url: ${{ secrets.REVIEW_API_URL }}
          api-token: ${{ secrets.REVIEW_API_TOKEN }}
```

Экшен сам вытащит diff между base/head коммитами PR'а, отправит его в
`/review` вашего сервиса и (best-effort, на стороне сервиса) запостит
комментарий с находками обратно в PR.

## Входные параметры

| Параметр | Обязательный | По умолчанию | Описание |
|---|---|---|---|
| `api-url` | да | — | URL вашего Review API сервиса |
| `api-token` | да | — | Bearer-токен для аутентификации запроса к сервису |
| `github-token` | нет | `${{ github.token }}` | Токен для постинга комментариев в PR |

## Версионирование

Тег `v1` указывает на последнюю совместимую minor/patch версию из `1.x.x`
(стандартная практика для GitHub Actions). Для точной версии используйте
`@v1.0.0`.

## Связанные репозитории

- [llm-code-review](https://github.com/TuychiyevNodirbek/llm-code-review) — сам сервис (FastAPI, RAG, LLM-роутер)
