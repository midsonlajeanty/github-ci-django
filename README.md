# Github Actions CI for Django Application

This is a basic workflow to help you start the actions to implement a continuous integration pipeline for a Django application using PostgreSQL.


### Set up the name of your pipeline

```yaml
name: CI
```

### Added event triggers to specify when the workflow should be triggered

```yaml
on:
  push:
    branches:
      - "*"
  pull_request:
    branches:
      - "*"
```
`"*"` for all branch

you can specify a branch name

```yaml
push:
    branches:
      - master
```

or the name of several branches
```yaml
push:
    branches: ["master", "dev"]
```

### Set up Jobs
A workflow run is made up of one or more jobs that can run sequentially or in parallel
```yaml
jobs:
    # This workflow contains a single job called "build"
  build:
    # The type of runner on which the work will be executed and the definition of the run strategies
    runs-on: ubuntu-latest
    strategy:
      max-parallel: 4
      matrix:
        python-version: [3.x]
    
```

### Define your environment variables so that your application can connect to the database
```yaml
env:
  DB_NAME: postgres
  DB_USER: postgres
  DB_PASSWORD: password
  DB_HOST: localhost
  DB_PORT: 5432
```
### Added the PostgreSQL Database Service via a docker image
```YAML
services:
  postgres:
    image: postgis/postgis:14-master
    env:
      POSTGRES_PASSWORD: password
    ports:
    - 5432:5432
```
The `POSTGRES_PASSWORD` variable is required to make the database functional


### Set up Steps
Steps represent a sequence of tasks that will be executed as part of the job
```yaml
steps:
# Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
- uses: actions/checkout@v3
- name: Set up Python ${{ matrix.python-version }}
    uses: actions/setup-python@v3
    with:
    python-version: ${{ matrix.python-version }}

- name: Install Dependencies
    run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt

- name: Run Tests
    run: |
    python manage.py test

```
