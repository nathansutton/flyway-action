# Flyway Action

This action runs [Flyway v9][flyway] to migrate database. It aims to migrate database for testing in workflows.

It is based on the excellent work of [@joshuaavalon](https://github.com/joshuaavalon/flyway-action).

The main addition for this fork is to add a schemas argument to allow for non-public flyway migrations.

## Usage

```yaml
name: Main
on:
  - push
jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres
        env:
          POSTGRES_DB: db
          POSTGRES_USER: user
          POSTGRES_PASSWORD: password
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    steps:
      - uses: actions/checkout@v2
      - uses: nathansutton/flyway-action@v9
        with:
          url: jdbc:postgresql://postgres:5432/db
          user: user
          password: password
      - run: echo 'testing'
```

Currently, it supports `url`, `user`, `password`, `initSql`, `schemas`, and `locations`. `locations` are default to `filesystem:./sql`.

For details, please check out Flyway [documentation].

[flyway]: https://flywaydb.org/
[documentation]: https://flywaydb.org/documentation/configuration/parameters/
