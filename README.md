# LoadScan
LoadScan is a [Taurus](https://gettaurus.org/) script that generates load on different API endpoints, and measures the performance of these endpoints.

## Requirements
Get started by installing [Taurus](https://gettaurus.org/) and its dependencies.

    sudo apt-get update
    sudo apt-get install python3 default-jre-headless python3-tk python3-pip python3-dev \
    libxml2-dev libxslt-dev zlib1g-dev net-tools
    pip install --user bzt

The last command will install [JMeter](https://jmeter.apache.org/) and all other requirements.

## Run

### Local Execution

1. `cp secrets.example.yml secrets.yml`
1. Edit `secrets.yml` to contain the needed configuration information
1. `cp accounts.example.csv accounts.csv`
1. Edit `accounts.csv` to contain valid login credentials for the application. *NOTE*: This is only required for workflows that use the `login-preexisting` scenario.
1. Set the scenario that you want to run in `run.yml`.
1. Point Taurus to `run.yml`

    ```
    bzt run.yml
    ```
The script writes artifacts and `jmeter.log` in the root directory. To debug the running of a particuar scenario, open the JMeter gui when running the test.

    bzt -gui run.yml

## Blazemeter execution

[Blazemeter](https://www.blazemeter.com/) makes a lot of assumptions about your test. Therefore, the easiest way to execute a Taurus script on Blazemeter is to put everyone in one file and upload that, along with needed csv and data files. A convenient way to do this is to kick of a local run (instructions above) and harvest `effective.yml` from the artifacts directory. Most of this file can be deleted, except the following keys: scenarios, settings, execution. Ensure that you have the desired concurrency configured before uploading to Blazemeter. Once your done cleaning it up, the file should look something like this (but with more scenarios).

```
---
scenarios:
  create-account:
    requests:
    - set-variables:
        email: perfbot+taurus${__RandomString(8,1234567890)}@opentechstrategies.com
        password: '********'
    - assert-jsonpath:
      - expected-value: 'true'
        jsonpath: $.isSuccessful
        validate: true
      body: '{"RequestVO":{"data":[{"AccountVO":{"primaryEmail":"${email}","fullName":"${email}","agreed":true,"optIn":false},"AccountPasswordVO":{"password":"${password}","passwordVerify":"${password}"}}],"apiKey":"my-api-key","csrf":"my-csrf-token"},"timeout":{}}'
      headers:
        content-type: application/json
      label: create-user-account
      method: POST
      url: https://example.opentechstratgies.com/api/account/post
settings:
  aggregator: consolidator
  default-executor: jmeter
  env:
    API_KEY: my-api-key
    APP_URL: https://example.opentechstrategies.com
execution:
- concurrency: 10
  executor: jmeter
  hold-for: 5m
  ramp-up: 1m
  scenario: create-account
```

## Project Structure

This project uses the following files and directories during execution:
- `actions.yml`: discrete API calls and blocks of functionality (e.g. login, upload a file)
- `scenarios.yml`: user stories, constructed from actions (e.g. A user logs in, uploads a file, and waits for the file to become viewable)
- `run.yml`: The top-level run file, which specifies the run duration, concurrency, user story/load test scenario to run, and reporting options
- `secrets.yml`: Sensitive required fields e.g. API keys
- `accounts.csv`: The login credentials for various user accounts (**OPTIONAL**: only required for `login-prexisting` scenarios)
- `files.csv`: The names of files to upload (**OPTIONAL**: only required for `fileupload` scenarios)

## Scenarios

Currently, the following workflows are available as scenarios for load testing.

- `login-preexisting-fileupload`: Login with user credentials specified in `accounts.csv` and upload files repeatedly, without polling for record status. This scenario is a convenient way to upload a large number of files to a small list of specified user accounts quickly.
- `login-fileupload-poll`: Create a new user account, login with that account, upload a file, and poll the record until processing has completed, then upload more files.
- `create-account-login`: Create an account, login, then logout. Repeat.

## Linting

The yaml files are linted with [yamllint](https://github.com/adrienverge/yamllint).
