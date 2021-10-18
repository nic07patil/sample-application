# mySuperMon-service

![GitHub release (latest by date)](https://img.shields.io/github/v/release/keptn-contrib/mysupermon-service?include_prereleases)

The *mysupermon-service* is a [Keptn](https://keptn.sh) service that is responsible for collecting datatbase statistics performed while test sequence executed using [mySuperMon](https://app.mysupermon.com) endpoints.

We are going to install the service in the same cluster that Keptn is running in.
Checkout also the [installation option for Keptn on K3s](https://github.com/keptn-sandbox/keptn-on-k3s).

## Installation

### Deploy mySuperMon database Agent

1. Create a mysupermon-agent.yml file and copy below yml content paste into it.  and IMAGE_NAME with image name you need (), you can find myspermon agent images here [mySuperMon Docker hub](https://hub.docker.com/u/mysupermon)

    **NOTE**
    1. Replace AGENT_DATABASE_NAME with the agent supported database name
    1. Replace IMAGE_NAME with image name you need (MySQL, PostgreSQL, MariaDB, MSSQL, ORACLE, MongoDB), you can find myspermon agent images here [mySuperMon Docker hub](https://hub.docker.com/u/mysupermon)
    1. Replace port no with agent supported port you can find it on it's docker hub overview tab.

    ![](./images/docker-agent-port.png)



    ```yml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
        name: AGENT_DATABASE_NAME-agent
    spec:
        replicas: 1
        selector:
            matchLabels:
            app: AGENT_DATABASE_NAME-agent
        template:
            metadata:
                labels:
                app: AGENT_DATABASE_NAME-agent
            spec:
                containers:
                - name: AGENT_DATABASE_NAME-agent
                image: mysupermon/IMAGE_NAME:latest
                ports:
                - containerPort: 8111  
    ```

1. Apply the mysupermon-agent.yml file using below command in the in the same cluster that Keptn is running in.

    ```
    kubectl apply -f mysupermon-agent.yml -n keptn
    ```

1. After deploying the agent check the logs of agent using below command and copy Agent unique id

    ```
    kubectl logs -f deployment/AGENT_DEPLOYMENT_NAME -n keptn
    ```

    ```
    ################################################################
    ########## Agent Unique Id : 9604729ADE98MONGO-AGENT-DEPLOY-77C99D9885-9D882190-MONGODB ################
    ################################################################

    ```


### Add Agent, Application Name and Database

1. Visit to [mySuperMon](https://app.mysupermon.com) and log in.

1. Click on *Agent -> Agent Management* in the side menu and fill the form. Paste the Agent Unique Id in the *Agent Unique Id* field.

![](./images/add-agent.png)

1. Click on *save* to add agent.

1. Click on *Application -> Application Management* and click on the *New Application* Button on the page.

![](https://app.mysupermon.com/assets/images/docs/mysql/config-app/config1.png)

1. Fill the form and click on *save* to add application.

![](https://app.mysupermon.com/assets/images/docs/mysql/config-app/config2.png)

1. Now click on the added application and then click on *Add New Datasource* button to add database.

![](https://app.mysupermon.com/assets/images/docs/mysql/config-app/config2.png)  ![](https://app.mysupermon.com/assets/images/docs/mysql/config-app/config2.png)

1. Fill the form by selecting driver type , Agent, Enter Database username, Database password, Host url of Database, Port no. on whitch database is running, database name and email.

```
# You can find Host Url using this command
kubectl get pods -o wide -n sockshop-dev

NAME                        READY   STATUS    RESTARTS   AGE    IP                      NODE                  NOMINATED NODE   READINESS GATES
carts-db-54b449c598-kxggb   1/1     Running   0          62d    10.42.1.20 <== HostUrl  k3d-mykeptn-agent-0   <none>           <none>
carts-76b56b96dd-g9djp      1/1     Running   0          3h6m   10.42.1.54              k3d-mykeptn-agent-0   <none>           <none>

```

1. Click on Test Connection after successful connection Click on save connection

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
3. 
