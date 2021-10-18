# mySuperMon-service

![GitHub release (latest by date)](https://img.shields.io/github/v/release/keptn-contrib/mysupermon-service?include_prereleases)

The *mysupermon-service* is a [Keptn](https://keptn.sh) service that is responsible for collecting datatbase statistics performed while test sequence executed using [mySuperMon](https://app.mysupermon.com) endpoints.

We are going to install the service in the same cluster that Keptn is running in.
Checkout also the [installation option for Keptn on K3s](https://github.com/keptn-sandbox/keptn-on-k3s).

## Installation

### Add application and it's database

1. Visit to [mySuperMon](https://app.mysupermon.com) and log in.

1. Watch this video to add application and it's datasource

[![mySuperMon](https://img.youtube.com/vi/YOUTUBE_VIDEO_ID_HERE/0.jpg)](https://www.youtube.com/watch?v=YOUTUBE_VIDEO_ID_HERE)

### Deploy mySuperMon Service

Now we will deploy the mySuperMon Service in the Keptn Cluster.

1. Create a secret using mySuperMon username and password, you can go ahead and copy/paste this next line. Make sure you are connected to the correct Kubernetes cluster.

    ```
    kubectl create secret generic mysupermon --from-literal="mysupermon_username=YOUR_MYSUPERMON_USERNAME" --from-literal="mysupermon_password=YOUR_MYSUPERMON_PASSWORD" -n keptn
    ```

1. Now clone the muysupermon service repo

    ```
    git clone https://github.com/keptn-sandbox/mySuperMon-service.git
    ```

2. Before applying deployment file add your KEPTN_ENDPOINT url and KEPTN_API_TOKEN to the service.yml 'env section'.

    ```yaml
    ...
    env:
        - name: KEPTN_ENDPOINT
          value: '' #TODO: Add your keptn api endpoint eg: http://1.2.3.4.nip.io/api
        - name: KEPTN_API_TOKEN
          value: '' #TODO: Add your keptn api token
        - name: MYSUPERMON_ENDPOINT
          value: 'https://app.mysupermon.com'
    ...
    ```
