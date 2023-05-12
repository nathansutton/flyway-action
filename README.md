# Flyway Action

This action runs [Flyway v9][flyway] to migrate database. It aims to migrate database for testing in workflows.

It is based on the excellent work of [@joshuaavalon](https://github.com/joshuaavalon/flyway-action).

The main addition for this fork is to add a schemas argument to allow for non-public flyway migrations.

## Sample continuous integration pipeline

Use a throwaway database on a pull request to ensure your migrations work in each schema

```yaml
name: ci
on:
  push:
    branches: 
      - main
jobs:
  ci:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:13
        env:
          POSTGRES_DB: postgres
          POSTGRES_USER: local
          POSTGRES_PASSWORD: password
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    steps:
      - uses: actions/checkout@v3
      - uses: nathansutton/flyway-action@v9
        with:
          url: jdbc:postgresql://postgres:5432/postgres
          user: local
          password: password
          locations: filesystem:./sql/schema1/
          schemas: schema1
      - uses: nathansutton/flyway-action@v9
        with:
          url: jdbc:postgresql://postgres:5432/postgres
          user: local
          password: password
          locations: filesystem:./sql/schema1/
          schemas: schema1

```

## Sample Continous Deploy Pipeline

On commits to your trunk, deploy the migrations for each schema automatically to your real database.
To use this, create an environment in github for production and assign the secrets 'url', 'user', and 'password' to environment.

```
name: app
on:
  pull_request:
    branches: 
      - main
jobs:
  deployment:
    runs-on: ubuntu-latest
    environment: production
    steps:
      - uses: actions/checkout@v2
      - uses: nathansutton/flyway-action@v9
        with:
          url: ${{ secrets.url }}
          user: ${{ secrets.user }}
          password: ${{ secrets.password }}
          locations: filesystem:./sql/schema1/
          schemas: schema1
      - uses: nathansutton/flyway-action@v9
        with:
          url: ${{ secrets.url }}
          user: ${{ secrets.user }}
          password: ${{ secrets.password }}
          locations: filesystem:./sql/schema2/
          schemas: schema2 
```


Currently, it supports `url`, `user`, `password`, `initSql`, `schemas`, and `locations`. `locations` are default to `filesystem:./sql`.

For details, please check out Flyway [documentation].

[flyway]: https://flywaydb.org/
[documentation]: https://flywaydb.org/documentation/configuration/parameters/
