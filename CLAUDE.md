# Working with Mastodon Glitch Edition

This document provides context and guidelines for AI assistants (like Claude) and developers working on the Mastodon Glitch Edition codebase.

## Project Overview

**Mastodon Glitch Edition** is a fork of [Mastodon](https://github.com/mastodon/mastodon), a free and open-source federated social network server based on ActivityPub. This fork includes additional features and customizations not found in upstream Mastodon.

- **License**: GNU Affero General Public License v3.0 (AGPLv3)
- **Documentation**: [glitch-soc.github.io/docs/](https://glitch-soc.github.io/docs/)
- **Contributing**: See [CONTRIBUTING.md](CONTRIBUTING.md)

## Tech Stack

### Backend
- **Ruby on Rails** (3.2+) - REST API and web pages
- **PostgreSQL** (14+) - Primary database
- **Redis** (7.0+) - Caching and session storage
- **Sidekiq** - Background job processing

### Frontend
- **Node.js** (20+) - Streaming API and build tools
- **React.js** - UI components
- **Redux** - State management

### Key Standards
- **ActivityPub** - Federation protocol for the Fediverse
- **OAuth2** - Authentication for third-party apps

## Directory Structure

```
mastodon/
├── app/
│   ├── controllers/     # Rails controllers
│   ├── models/          # ActiveRecord models
│   ├── services/        # Business logic
│   ├── workers/         # Sidekiq background jobs
│   ├── serializers/     # API response serializers
│   ├── javascript/      # Frontend React/Redux code
│   └── views/           # Rails view templates
├── config/              # Configuration files
├── db/                  # Database migrations and schema
├── lib/                 # Shared libraries and utilities
├── spec/                # RSpec tests (Ruby)
├── streaming/           # Node.js streaming server
└── public/              # Static assets
```

## Development Workflow

### Running Tests

```bash
# Ruby tests
bundle exec rspec

# JavaScript tests
yarn test

# Linting
bundle exec rubocop
yarn lint
```

### Code Style

- **Ruby**: Follow Rubocop rules (`.rubocop.yml`)
- **JavaScript**: Follow ESLint rules
- **Locale files**: Normalize with `i18n-tasks normalize`

### Database

- Use migrations for all schema changes
- Test migrations both up and down
- PostgreSQL-specific features are acceptable

## Important Conventions

### Commit Messages

Follow the [Keep a Changelog](https://keepachangelog.com/) convention:
- Use present tense verbs: "Add", "Change", "Fix", "Remove", "Deprecate"
- Write for end users/admins, not just developers
- Be specific and descriptive

Examples:
- Good: "Fix nil error when removing statuses caused by race condition"
- Poor: "Fixed NoMethodError in RemovalWorker"

### Pull Requests

- Keep PRs focused and small
- Link to existing issues when applicable
- Ensure CI passes (tests, linting, etc.)
- Update documentation for API changes
- Write PR descriptions from user/admin perspective

## Common Tasks

### Adding a New API Endpoint

1. Create/update controller in `app/controllers/api/`
2. Add routes in `config/routes.rb`
3. Create serializer in `app/serializers/`
4. Add tests in `spec/controllers/`
5. Update API documentation repository

### Adding a Frontend Feature

1. Create components in `app/javascript/mastodon/`
2. Add Redux actions/reducers if needed
3. Update locale files for translations
4. Add tests in `app/javascript/mastodon/__tests__/`
5. Consider accessibility (a11y) requirements

### Database Changes

1. Generate migration: `rails g migration MigrationName`
2. Write both `up` and `down` methods
3. Test migration on sample data
4. Update schema documentation

## Security Considerations

- Always validate and sanitize user input
- Be cautious with SQL queries (prevent injection)
- Follow OAuth2 best practices
- Check authorization before data access
- Report security issues via [SECURITY.md](SECURITY.md)

## Testing Requirements

All code changes should include:
- **Unit tests** for models, services, and utilities
- **Integration tests** for API endpoints
- **Component tests** for React components
- Tests should pass locally before committing

## Glitch-Specific Features

This fork includes features not in upstream Mastodon. When working on these:
- Document in [glitch-soc/docs](https://github.com/glitch-soc/docs)
- Consider maintainability during upstream merges
- Discuss major changes in GitHub Issues first

## Resources

- [Main Documentation](https://glitch-soc.github.io/docs/)
- [Development Guide](docs/DEVELOPMENT.md)
- [Federation Spec](FEDERATION.md)
- [Contributing Guidelines](CONTRIBUTING.md)
- [Code of Conduct](CODE_OF_CONDUCT.md)
- [Upstream Mastodon](https://github.com/mastodon/mastodon)

## Getting Help

- Check existing [GitHub Issues](https://github.com/glitch-soc/mastodon/issues)
- Review glitch-soc documentation
- Join the development Discord (see CONTRIBUTING.md)
- Read upstream Mastodon documentation at [docs.joinmastodon.org](https://docs.joinmastodon.org)

## Tips for AI Assistants

- Always run tests after making changes
- Respect the AGPLv3 license requirements
- Follow existing code patterns and conventions
- Consider federation/ActivityPub implications
- Check both frontend and backend when making changes
- Be mindful of performance (this runs on federated servers)
- Remember that changes affect multiple users across instances
