# Azure Spring Apps Deployment

[//]: # (TODO: Remove 'where-for-dinner' from app names, it causes too long of deployment names)
[//]: # (TODO: Figure out how to force web servers buildpack for ui)

## Prerequisites

These instructions assume that you have an Azure Spring Apps Enterprise Tier service already provisioned

* [Azure CLI version 2.42.0 or higher](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Azure CLI spring extension](https://learn.microsoft.com/en-us/cli/azure/spring)

## Quick Start

This section provides a fast track installation of the "simplest" configuration of the Hungry application using the application accelerator and the instructions immediately below.  A more thorough description of the configuration and installation scenarios are describes in subsequent sections of this page

* Provision Azure Service Bus:

```shell
    az servicebus namespace create --name where-for-dinner
    az servicebus topic create --namespace-name where-for-dinner --name where-for-dinner-search-result
    az servicebus topic subscription create --namespace-name where-for-dinner --topic-name where-for-dinner-search-result --name where-for-dinner-notify-group
```

* Create Apps

```shell
    az spring app create -n availability
    az spring app create -n crawler
    az spring app create -n notify
    az spring app create -n search
    az spring app create -n search-proc
    az spring app create -n app-ui
```

* Create App Connections

```shell
    az spring connection create servicebus --app availability --tg asa --namespace where-for-dinner --connection-string -g asa --client-type springBoot
    az spring connection create servicebus --app notify --tg asa --namespace where-for-dinner --connection-string -g asa --client-type springBoot
```

* Deploy Apps

```shell
    az spring app deploy -n availability --source-path where-for-dinner-availability
    az spring app deploy -n crawler --source-path where-for-dinner-crawler
    az spring app deploy -n notify --source-path where-for-dinner-notify
    az spring app deploy -n search --source-path where-for-dinner-search
    az spring app deploy -n search-proc --source-path where-for-dinner-search-proc
    az spring app deploy -n search-proc --source-path where-for-dinner-search-proc
    az spring app deploy -n app-ui --source-path where-for-dinner-ui --build-env BP_WEB_SERVER=nginx BP_NODE_RUN_SCRIPTS=build BP_WEB_SERVER_ROOT=build BP_WEB_SERVER_ENABLE_PUSH_STATE=true
```

* Define Gateway Routes

```shell
    az spring gateway route-config create --name availability-routes --app-name availability --routes-file routes/where-for-dinner_availability_route.json
    az spring gateway route-config create --name search-routes --app-name search --routes-file routes/where-for-dinner_search_route.json
    az spring gateway route-config create --name app-ui-routes --app-name ui --routes-file routes/where-for-dinner_ui_routes.json
```