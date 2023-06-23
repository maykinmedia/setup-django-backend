# setup-django-backend Github workflow action

This composite workflow action takes care of some boilerplate for Maykin's Django
projects:

* Setting up Python (with pip cache)
* Installing backend dependencies
* Tuning PostgreSQL for faster test running
* Setting up NodeJS and npm + building the client-side code

## Example usage:

```yaml
jobs:
  tests:
    name: Run the Django test suite
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:14
        env:
          POSTGRES_HOST_AUTH_METHOD: trust
        ports:
          - 5432:5432
        # Needed because the postgres container does not provide a healthcheck
        options:
          --health-cmd pg_isready --health-interval 10s --health-timeout 5s --health-retries 5
          --name postgres
      redis:
        image: redis:6
        ports:
          - 6379:6379
    steps:
      - uses: actions/checkout@v3
      - name: Set up backend environment
        uses: maykinmedia/setup-django-backend@main
        with:
          apt-packages: 'libxml2-dev libxmlsec1-dev libxmlsec1-openssl gettext postgresql-client'
          python-version: '3.10'
          optimize-postgres: 'yes'
          pg-service: 'postgres'
          setup-node: 'yes'
          npm-ci-flags: '--legacy-peer-deps'

      - name: Run tests
        run: ...
```
