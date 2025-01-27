# CI/CD Integration

Run Globstar in your CI/CD pipeline to enforce checkers on every commit and pull request. By default, `globstar check` will run all builtin checkers an all checkers discovered in the `.globstar` directory in the root of your repository.

## GitHub Actions

Add the following workflow to your repository to run Globstar on every push and pull request to the `main` branch.

```yaml
# .github/workflows/globstar.yml

name: Run Globstar Analysis
on:
  pull_request:
  push:
    branches: [ main ]
jobs:
  analyze:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install Globstar
        run: curl -sSL https://get.globstar.dev | sh
      - name: Run Globstar checks
        run: ./bin/globstar check
```

## GitLab CI

Add this job to your `.gitlab-ci.yml` file:

```yaml
# .gitlab-ci.yml

globstar:
  image: golang:latest
  stage: test
  script:
    - curl -sSL https://get.globstar.dev | sh
    - ./bin/globstar check
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

## CircleCI

Add this job to your `.circleci/config.yml` file:

```yaml
# .circleci/config.yml

version: 2.1
jobs:
  globstar:
    docker:
      - image: cimg/base:stable
    steps:
      - checkout
      - run:
          name: Install Globstar
          command: curl -sSL https://get.globstar.dev | sh
      - run:
          name: Run Globstar checks
          command: ./bin/globstar check

workflows:
  check:
    jobs:
      - globstar
```

## Azure Pipelines

Add this configuration to your `azure-pipelines.yml` file:

```yaml
# azure-pipelines.yml

trigger:
  - main

pool:
  vmImage: 'ubuntu-latest'

steps:
- checkout: self

- script: curl -sSL https://get.globstar.dev | sh
  displayName: 'Install Globstar'

- script: ./bin/globstar check
  displayName: 'Run Globstar checks'
```

## Bitbucket Pipelines

Add this configuration to your `bitbucket-pipelines.yml` file:

```yaml
# bitbucket-pipelines.yml

pipelines:
  default:
    - step:
        name: Run Globstar checks
        image: golang:latest
        script:
          - curl -sSL https://get.globstar.dev | sh
          - ./bin/globstar check
  pull-requests:
    '**':
      - step:
          name: Run Globstar checks
          image: golang:latest
          script:
            - curl -sSL https://get.globstar.dev | sh
            - ./bin/globstar check
```

## Jenkins Pipeline

Add this configuration to your `Jenkinsfile`:

```groovy
// Jenkinsfile
pipeline {
    agent any

    stages {
        stage('Install Globstar') {
            steps {
                sh 'curl -sSL https://get.globstar.dev | sh'
            }
        }
        stage('Run Globstar checks') {
            steps {
                sh './bin/globstar check'
            }
        }
    }
}
```

> [!NOTE]
> This pipeline assumes that you have [Declarative Pipeline](https://plugins.jenkins.io/pipeline-model-definition/) enabled in your Jenkins instance.

Each configuration runs Globstar on the appropriate events (pull requests, merges to main branch) and uses the same basic flow — install Globstar using the installation script, then run the checks using `globstar check`.