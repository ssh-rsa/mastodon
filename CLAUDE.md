# Claude Code Development Guide

This document provides context and guidance for working on the Mastodon Glitch Edition codebase with Claude Code.

## Project Overview

Mastodon is a free, open-source social network server based on ActivityPub. This repository is **Mastodon Glitch Edition**, a fork of the main Mastodon project with additional features and customizations.

- **License**: AGPLv3
- **Primary Language**: Ruby (Rails), JavaScript (React)
- **Documentation**: [glitch-soc.github.io/docs/](https://glitch-soc.github.io/docs/)

## Tech Stack

### Backend

- **Ruby on Rails** 3.2+ - REST API and web pages
- **PostgreSQL** 14+ - Main database
- **Redis** 7.0+ - Caching and queueing
- **Sidekiq** - Background job processing

### Frontend

- **Node.js** 20+
- **React.js** - UI components
- **Redux** - State management
- **Vite** - Build tool (see `vite.config.mts`)

## Project Structure

```
mastodon/
├── app/                    # Rails application code
│   ├── controllers/        # API and web controllers
│   ├── models/             # ActiveRecord models
│   ├── services/           # Business logic services
│   ├── workers/            # Sidekiq background workers
│   ├── javascript/         # React frontend code
│   │   ├── mastodon/       # Main Mastodon app
│   │   └── styles/         # SCSS styles
│   └── views/              # Rails views
├── config/                 # Configuration files
│   ├── locales/            # i18n translations
│   └── routes.rb           # Rails routes
├── db/                     # Database migrations and schema
├── lib/                    # Library code and modules
├── spec/                   # RSpec tests
├── streaming/              # Node.js streaming server
└── docs/                   # Documentation
    ├── DEVELOPMENT.md      # Development setup
    └── COLOR_CUSTOMIZATION.md  # Theme customization
```

## Key Files

- `Gemfile` - Ruby dependencies
- `package.json` - Node.js dependencies
- `config/routes.rb` - API and web routes
- `db/schema.rb` - Database schema
- `.env.production.sample` - Environment variable reference
- `docker-compose.yml` - Docker development setup

## Development Workflow

### Running Tests

```bash
# Ruby tests (RSpec)
bundle exec rspec

# JavaScript tests (Vitest)
yarn test

# Linting
bundle exec rubocop
yarn lint
```

### Database Operations

```bash
# Run migrations
bundle exec rails db:migrate

# Rollback
bundle exec rails db:rollback

# Reset database
bundle exec rails db:reset
```

### Asset Compilation

```bash
# Development
yarn dev

# Production build
yarn build
```

## Code Conventions

### Ruby

- Follow the RuboCop configuration (`.rubocop.yml`)
- Use services for complex business logic (`app/services/`)
- Keep controllers thin, models focused
- Write RSpec tests for new features

### JavaScript

- Follow ESLint configuration (`eslint.config.mjs`)
- Use functional components with hooks
- Follow Redux patterns for state management
- Use TypeScript types where defined

### Commits

- Write clear, descriptive commit messages
- Reference issues when applicable
- Keep commits focused and atomic

## Common Tasks

### Adding a New API Endpoint

1. Add route in `config/routes.rb`
2. Create/update controller in `app/controllers/api/`
3. Add service logic in `app/services/` if needed
4. Write tests in `spec/requests/`

### Adding a Frontend Feature

1. Create components in `app/javascript/mastodon/features/`
2. Add Redux actions/reducers if needed
3. Update relevant containers
4. Add styles in `app/javascript/styles/`
5. Write tests in `spec/javascript/` or `.test.js` files

### Database Changes

1. Generate migration: `rails g migration MigrationName`
2. Edit migration in `db/migrate/`
3. Run migration: `rails db:migrate`
4. Update model in `app/models/`
5. Add tests

## Important Notes

### Federation

- Mastodon is a federated system using ActivityPub
- Changes to federation logic require careful consideration
- See `FEDERATION.md` for more details

### Security

- This is security-critical software
- Always sanitize user input
- Be careful with XSS, SQL injection, and other OWASP Top 10 vulnerabilities
- Review `SECURITY.md` for security policies

### Performance

- Background jobs should use Sidekiq workers
- Heavy operations should be async
- Database queries should be optimized (use `includes` to avoid N+1)

### i18n

- All user-facing strings should be internationalized
- Use `I18n.t()` in Ruby, `FormattedMessage` in React
- Translations are managed via Crowdin

## Resources

- [Development Setup](docs/DEVELOPMENT.md)
- [Contributing Guidelines](CONTRIBUTING.md)
- [Color Customization](docs/COLOR_CUSTOMIZATION.md)
- [Official Mastodon Docs](https://docs.joinmastodon.org)
- [Glitch-soc Docs](https://glitch-soc.github.io/docs/)

## Getting Help

- Check existing documentation first
- Search issues on GitHub
- Review recent commits for similar changes
- Consult the Glitch-soc community

## Tips for Claude Code

- Use `Grep` to search for similar patterns before implementing new features
- Check both Ruby and JavaScript code when making changes to features
- Review tests to understand expected behavior
- Look at recent migrations to understand database structure
- Check `app/services/` for existing business logic that might be reusable
