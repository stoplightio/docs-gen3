# Installation guide

## Overview 

Stoplight consists of three container services:

- Stoplight Frontend
- Stoplight Backend
- A PostgreSQL database

## Deployment environments

Stoplight supports the following deployment environments:

- OpenShift
- Unix servers

This installation guide covers how to deploy to OpenShift.

## General configuration guide

### Resource requirements

> Note: these are estimates that will vary based on your data and usage.

- Postgres: 10GB storage, 4GB memory
- Stoplight Backend: 4GB memory
- Stoplight Frontend: 4GB memory


### Environment variables

Use the following environment variables to configure our containers.

#### The Postgres container

> Note: these variables are for the official DockerHub postgres container. The OpenShift catalog postgres container uses slightly different variables.

| Variable name | Example | Description | Notes |
| ------------- | ------- | ----------- | ----- |
| POSTGRES_DB   | stoplight | The initial POSTGRES database to create | Must match the value in the stoplight backend's SL_POSTGRES_URL |
| POSTGRES_USER | stoplight | The initial POSTGRES user to create | Must match the value in the stoplight backend's SL_POSTGRES_URL |
| POSTGRES_PASSWORD | stoplight | The password for POSTGRES_USER | |

#### The Stoplight backend container

| Variable name | Example | Description | Notes |
| ------------- | ------- | ----------- | ----- |
| SL_POSTGRES_URL   | postgres://stoplight:stoplight@postgres.example.com:5432/stoplight | Postgres username, password, URL, and database for the stoplight backend to use | Must match the values configured for Postgres |
| SL_APP_SECRET | cgL49eK8cigD8SOyLzaI | The secret seed used to create and verify tokens | |

#### The Stoplight frontend container

| Variable name | Example | Description | Notes |
| ------------- | ------- | ----------- | ----- |
| SL_API_URL   | https://stoplight-backend.example.com:443 | The URL used to reach the Stoplight backend from the browser client. | |

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

    > Note: this is an official Docker image. The official OpenShift catalog image for Postgres will also work.

    - [quay.io/stoplight/frontend:stable](https://quay.io/stoplight/frontend:stable)
    - [quay.io/stoplight/backend:stable](https://quay.io/stoplight/backend:stable)

    > Note: as an alternative to creating image streams that follow dynamic tags (ex. stable), you can [manually select a version](#manually-select-an-app-and-API-version) and watch the Stoplight JSON manifest for changes.
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
9. Create a deployment for the Stoplight backend. Refer to the [general configuration guide](#environment-variables) for environment variables.
    - Readiness probe: TCP check to port 4060.
    - Point SL_POSTGRES_URL at the Postgres service DNS name.
10. Create a service for the Stoplight backend:
    - Map port 443 to 4060.
    - Add a route with a hostname, for example `stoplight-backend.$company_name.com`. Configure TLS with a certificate for your domain.
11. Create a service for the Stoplight frontend:
    - Map port 443 to 4050.
    - Add a route with a hostname, for example `stoplight-backend.$company_name.com`. Configure TLS with a certificate for your domain.
12. Navigate to the Stoplight frontend endpoint. If all is well, you should see an initial installation page.




