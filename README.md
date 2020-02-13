# LoadScan
Semi-generic framework for testing server-side performance and scalability via a network API.

## Requirements

Get started by installing [Taurus](https://gettaurus.org/) and it's dependencies.

    sudo apt-get update
    sudo apt-get install python3 default-jre-headless python3-tk python3-pip python3-dev \
  libxml2-dev libxslt-dev zlib1g-dev net-tools
    pip install --user bzt

The last command will install [JMeter](https://jmeter.apache.org/) and all other requirements.

## Run

1. Modify `secrets.example.yml` with the needed configuration information
2. Rename `secrets.example.yml` to `secrets.yml`
3. Point Taurus to `test.yml` to run

    bzt test.yml

The script produces artifacts in the `artifacts/` directory, and `jmeter.log` output.
