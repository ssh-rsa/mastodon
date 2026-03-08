# CLAUDE.md — Mastodon (glitch-soc fork)

This file provides guidance for Claude Code when working in this repository.

## Project Overview

This is **Mastodon Glitch Edition** (glitch-soc), a feature-rich fork of [Mastodon](https://joinmastodon.org), a federated social network server built on ActivityPub. The stack is a Rails backend + React frontend + Node.js streaming server.

- **Upstream**: https://github.com/glitch-soc/mastodon
- **Version**: v4.5.5
- **License**: AGPL-3.0-or-later

---

## Tech Stack

| Layer          | Technology                                  |
| -------------- | ------------------------------------------- |
| Backend        | Ruby 3.4.7, Rails 8.0, Puma 7.0             |
| Frontend       | React 18.2, Redux, TypeScript 5.9, Vite 7.1 |
| Streaming      | Node.js 24.10, Express, WebSockets          |
| Database       | PostgreSQL 14+                              |
| Cache / Queue  | Redis 7+, Sidekiq                           |
| Search         | Elasticsearch 7.10.2 (optional)             |
| Testing (Ruby) | RSpec, Fabrication, Capybara                |
| Testing (JS)   | Vitest, @testing-library/react, Playwright  |

---

## Development Setup

The repo ships a VS Code Dev Container. Start all services with:

```bash
bin/setup          # install gems, JS packages, prepare DB (run once)
bin/dev            # starts web + sidekiq + streaming + vite via Foreman/Overmind
```

**Procfile.dev processes:**

- `web` → Puma on port 3000
- `sidekiq` → background job worker
- `stream` → Node.js streaming API on port 4000
- `vite` → Vite HMR dev server on port 3036

**Dev container services** (via `.devcontainer/compose.yaml`):

- PostgreSQL 14 → port 5432
- Redis 7 → port 6379
- Elasticsearch 7.10.2 → port 9200
- LibreTranslate → port 5000

---

## Common Commands

### Ruby / Rails

```bash
bin/rails server            # start web server
bin/rails console           # Rails REPL
bin/rails db:migrate        # run pending migrations
bin/rails db:schema:load    # load schema from scratch
bin/rake mastodon:setup     # interactive initial setup wizard
```

### Testing

```bash
bin/rspec                   # run all Ruby tests
bin/rspec spec/models/      # run specific test directory
yarn test:js                # run all JS tests (Vitest)
yarn test:js -- --watch     # Vitest watch mode
yarn storybook              # Storybook component dev server
```

### Linting & Formatting

```bash
bin/rubocop                 # Ruby linting
bin/rubocop -a              # auto-fix safe offenses
yarn lint:js                # ESLint
yarn lint:css               # Stylelint
yarn format                 # Prettier (write)
yarn format:check           # Prettier (check only)
bundle exec i18n-tasks missing  # check for missing translations
```

### JavaScript / Frontend

```bash
yarn install                # install JS dependencies
yarn build                  # production asset build via Vite
yarn build-storybook        # build static Storybook
```

---

## Architecture

### Backend (`app/`)

```
app/
├── controllers/    # Rails controllers (API, admin, auth, web)
├── models/         # ActiveRecord models (~50+)
├── workers/        # Sidekiq background jobs
├── services/       # Business logic service objects
├── serializers/    # JSON serializers for API responses
├── policies/       # Pundit authorization policies
├── chewy/          # Elasticsearch index definitions
└── javascript/     # React/TypeScript frontend (see below)
```

**Key patterns:**

- Service objects in `app/services/` encapsulate business logic (prefer these over fat controllers/models)
- Pundit policies in `app/policies/` control authorization
- Sidekiq workers in `app/workers/` handle async tasks (federation, notifications, cleanup)
- API controllers live under `app/controllers/api/`

### Frontend (`app/javascript/`)

```
app/javascript/
├── mastodon/       # core React application (shared)
├── flavours/
│   ├── glitch/     # glitch-soc enhanced UI
│   └── vanilla/    # upstream Mastodon UI
├── entrypoints/    # Vite entry points
├── styles/         # SCSS stylesheets
└── testing/        # test utilities and setup
```

**Two UI flavours**: `glitch` (this fork's enhanced UI) and `vanilla` (upstream-compatible). Most new glitch-soc features live under `flavours/glitch/`.

**TypeScript path aliases** (from `tsconfig.json`):

- `@/*` → `app/javascript/*`
- `mastodon/*` → `app/javascript/mastodon/*`
- `flavours/glitch/*` → `app/javascript/flavours/glitch/*`

### Streaming Server (`streaming/`)

Standalone Node.js server that handles WebSocket connections for real-time timelines. Connects directly to PostgreSQL and Redis. Entry point: `streaming/index.js`.

### Key Files

| File                      | Purpose                                           |
| ------------------------- | ------------------------------------------------- |
| `config/routes.rb`        | Main Rails router (delegates to `config/routes/`) |
| `app/models/account.rb`   | Core Account model                                |
| `app/models/status.rb`    | Core Status (post) model                          |
| `lib/mastodon/version.rb` | Version constants                                 |
| `vite.config.mts`         | Vite build configuration                          |
| `vitest.config.mts`       | Vitest dual project setup (jsdom + Playwright)    |
| `eslint.config.mjs`       | ESLint flat config                                |
| `.rubocop.yml`            | Rubocop rules + plugin configs                    |

---

## Code Conventions

### Ruby

- Follow existing Rubocop rules (`.rubocop.yml` with 6 plugins)
- Use service objects for business logic — avoid fat models/controllers
- Prefer `frozen_string_literal: true` (enforced by Rubocop)
- Database queries should be scoped; avoid N+1 (use `.includes`, `.preload`, `.eager_load`)
- New migrations go in `db/migrate/`; non-blocking post-deploy migrations in `db/post_migrate/`

### JavaScript / TypeScript

- Prefer TypeScript (`.ts`/`.tsx`) for all new code
- ESLint enforces rules for accessibility (jsx-a11y), imports, React, and formatjs
- Use `useCallback`/`useMemo` judiciously; avoid premature optimization
- i18n: all user-visible strings must use `react-intl` (`FormattedMessage`/`useIntl`)
- CSS: SCSS with BEM-style naming; colocate component styles

### Git & PRs

- Branch naming: descriptive slugs (e.g., `kylia/v4.5.5`)
- Commit messages: imperative mood, concise summary line
- PRs must pass: RSpec, Vitest, Rubocop, ESLint, Stylelint, i18n-tasks, Prettier
- Translations are managed via Crowdin — do not submit new locale files directly

---

## CI / CD

GitHub Actions workflows (`.github/workflows/`):

| Workflow                    | What it checks                  |
| --------------------------- | ------------------------------- |
| `test-ruby.yml`             | RSpec (test + production modes) |
| `test-js.yml`               | Vitest                          |
| `lint-ruby.yml`             | Rubocop                         |
| `lint-js.yml`               | ESLint                          |
| `lint-css.yml`              | Stylelint                       |
| `format-check.yml`          | Prettier                        |
| `check-i18n.yml`            | Missing/unused translation keys |
| `codeql.yml`                | Security scanning               |
| `bundler-audit.yml`         | Gem vulnerability scanning      |
| `build-container-image.yml` | Docker build                    |

---

## Environment & Configuration

- `.env.development` — local dev secrets (not committed upstream)
- `.env.production.sample` — template for production configuration
- `config/database.yml` — PostgreSQL primary + replica setup
- `config/environments/` — per-environment Rails config

---

## Glitch-soc Specifics

This fork adds features on top of upstream Mastodon:

- **Flavours system**: two complete UI flavours (`glitch` / `vanilla`) selectable per user
- **Advanced settings**: content warning defaults, posting scopes, rich text, etc.
- **Skins**: custom CSS themes under `app/javascript/skins/`
- **Additional posting options**: formatting, local-only posts, threaded mode
- Glitch-soc docs: https://glitch-soc.github.io/docs/

When merging upstream Mastodon changes, conflicts most often arise in:

- `app/javascript/flavours/glitch/` (glitch UI diverges from vanilla)
- `config/locales/` (translation additions)
- `config/routes.rb` / `config/routes/`
