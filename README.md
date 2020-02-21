# LoadScan
Semi-generic framework for testing server-side performance and scalability via a network API.

## Requirements
Get started by installing [Taurus](https://gettaurus.org/) and its dependencies.

    sudo apt-get update
    sudo apt-get install python3 default-jre-headless python3-tk python3-pip python3-dev \
    libxml2-dev libxslt-dev zlib1g-dev net-tools
    pip install --user bzt

The last command will install [JMeter](https://jmeter.apache.org/) and all other requirements.

## Run

1. `cp secrets.example.yml secrets.yml`
1. Edit `secrets.yml` to contain the needed configuration information
1. `cp accounts.example.csv accounts.csv`
1. Edit `accounts.csv` to contain valid login credentials for the application.
1. Point Taurus to `run.yml`

    ```
    bzt run.yml
    ```
The script writes artifacts to the `artifacts/` directory, and a `jmeter.log` in the root directory. To debug the running of a particuar scenario, open the JMeter gui when running the test.

    bzt -gui run.yml

## Project Structure

This project uses the following files and directories during execution:
- `actions.yml`: discrete API calls and blocks of functionality (e.g. login, upload a file)
- `scenarios.yml`: user stories, constructed from actions (e.g. A user logs in, uploads a file, and waits for the file to become viewable)
- `run.yml`: The top-level run file, which specifies the run duration, concurrency, user story/load test scenario to run, and reporting options
- `secrets.yml`: Sensitive required fields e.g. API keys
- `accounts.csv`: The login credentials for different user accounts
- `files.csv`: The names of files to upload
- `data/`: The directory where test data files are stored

## Provided Test Data

The provided test files are all taken from the public domain. Sources: https://archive.org, https://pypi.org, and the author of this project (@xmunoz).
