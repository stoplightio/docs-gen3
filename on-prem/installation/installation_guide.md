# Installation guide

## Overview 

Stoplight consists of three container services:

- The frontend app
- The backend API
- A PostgreSQL database

## Deployment environments

Stoplight supports the following deployment environments:

- OpenShift
- Unix servers

This installation guide covers how to deploy to OpenShift.

## General configuration guide

### Resource requirements

> Note: these are provisional and subject to change.

- Postgres: 1GB storage, 256M memory
- Backend API: 256M memory
- Frontend app: 256M memory


### Environment variables

Use the following environment variables to configure our containers.

#### The Postgres container

| Variable name | Example | Description | Notes |
| ------------- | ------- | ----------- | ----- |
| POSTGRES_DB   | stoplight | The initial POSTGRES database to create | Must match the value in the API's SL_POSTGRES_URL |
| POSTGRES_USER | stoplight | The initial POSTGRES user to create | Must match the value in the API's SL_POSTGRES_URL |
| POSTGRES_PASSWORD | stoplight | The password for POSTGRES_USER | |

#### The backend API container

| Variable name | Example | Description | Notes |
| ------------- | ------- | ----------- | ----- |
| SL_POSTGRES_URL   | Postgres://stoplight:stoplight@Postgres.example.com:5432/stoplight | Postgres username, password, URL, and database for the API to use | Must match the values configured for Postgres |
| SL_POSTGRES_SCHEMA | production | The Postgres schema to create and use |  |
| SL_APP_SECRET | cgL49eK8cigD8SOyLzaI | The secret seed used to create and verify tokens | |

#### The frontend app container

| Variable name | Example | Description | Notes |
| ------------- | ------- | ----------- | ----- |
| SL_API_URL   | https://stoplight-api.example.com:443 | The URL used to reach the API from the browser client. | |

## Deploy on OpenShift

### Manual installation

1. Get a Docker login and password from Stoplight to access our private quay.io repository. This is available from your technical account manager.
2. Create an OpenShift image pull secret with your credentials. For more information, refer to the [OpenShift documentation](https://docs.openshift.com/container-platform/3.4/dev_guide/managing_images.html#allowing-pods-to-reference-images-from-other-secured-registries).
3. Link the image pull secret to the default service account:
    ```
    oc secrets link default quay-pull-secret --for=pull
    ```
4. Create image streams for the following repositories:
    - [Postgres 11.2-alpine](https://github.com/docker-library/Postgres/tree/master/11).

    > Note: this is an official Docker image. The official OpenShift catalog image for the same Postgres version will also work.

    - [quay.io/stoplight/app-gen3:production](https://quay.io/stoplight/app-gen3:production)
    - [quay.io/stoplight/api-gen3:production](https://quay.io/stoplight/api-gen3:production)

    > Note: as an alternative to creating image streams for the app and API, you can [manually select a version](#manually-select-an-app-and-API-version) and watch the Stoplight JSON manifest for changes.
5. Create secrets for the secret environment variables you need to pass to each service. Refer to the [general configuration guide](#environment-variables) for the names and values of these variables.
6. Create a persistent storage asset for Postgres.
7. Create a deployment for Postgres, with the following settings:
    - Readiness probe: `/usr/libexec/check-container 5s delay, 1s timeout`
    - Liveliness probe: `/usr/libexec/check-container --live 120s delay, 10s timeout`
    - Mount: `$Postgres_data_volume → /var/lib/pgsql/data`  
    Replace `$Postgres_data_volume` with your data volume.
8. Create a service for Postgres:
    - Map port 5432 to 5432 on the pod.
    - Use the recommended hostname.
9. Create a deployment for the API. Refer to the [general configuration guide](#environment-variables) for environment variables.
    - Readiness probe: TCP check to port 4060.
    - Point SL_Postgres_URL at the Postgres service DNS name.
10. Create a service for the API:
    - Map port 443 to 4060.
    - Add a route with a hostname, for example `stoplight-api.$company_name.com`. Configure TLS with a certificate for your domain.
11. Create a service for the app:
    - Map port 443 to 4050.
    - Add a route with a hostname, for example `stoplight-api.$company_name.com`. Configure TLS with a certificate for your domain.
12. Navigate to the app endpoint. If all is well, you should see an initial installation page.



