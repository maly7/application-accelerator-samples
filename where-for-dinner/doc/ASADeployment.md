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
    az spring app create -n where-for-dinner-availability
    az spring app create -n where-for-dinner-crawler
    az spring app create -n where-for-dinner-notify
    az spring app create -n where-for-dinner-search
    az spring app create -n where-for-dinner-search-proc
    az spring app create -n where-for-dinner-ui
```

* Create App Connections

```shell
    az spring connection create servicebus --app where-for-dinner-availability --tg asa --namespace where-for-dinner --connection-string -g asa --client-type springBoot
    az spring connection create servicebus --app where-for-dinner-notify --tg asa --namespace where-for-dinner --connection-string -g asa --client-type springBoot
```

* Deploy Apps

```shell
    az spring app deploy -n where-for-dinner-availability --source-path where-for-dinner-availability
    az spring app deploy -n where-for-dinner-crawler --source-path where-for-dinner-crawler
    az spring app deploy -n where-for-dinner-notify --source-path where-for-dinner-notify
    az spring app deploy -n where-for-dinner-search --source-path where-for-dinner-search
    az spring app deploy -n where-for-dinner-search-proc --source-path where-for-dinner-search-proc
    az spring app deploy -n where-for-dinner-search-proc --source-path where-for-dinner-search-proc
    az spring app deploy -n where-for-dinner-ui --source-path where-for-dinner-ui
```

* Define Gateway Routes

```shell
    az spring gateway route-config create --name where-for-dinner_availability --app-name where-for-dinner-availability --routes-file routes/where-for-dinner_availability_route.json
    az spring gateway route-config create --name where-for-dinner_search --app-name where-for-dinner-search --routes-file routes/where-for-dinner_search_route.json
    az spring gateway route-config create --name where-for-dinner_ui --app-name where-for-dinner-ui --routes-file routes/where-for-dinner_ui_routes.json
```