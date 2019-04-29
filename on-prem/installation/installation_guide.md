# Installation guide

## Overview 

Stoplight Enterprise Dojo consists of three container services:

- The frontend app
- The backend API
- A PostgresQL database

## Deployment environments

Stoplight Dojo Enterprise  supports the following deployment environments:

- OpenShift
- Unix servers

## General configuration guide

### Resource requirements

PENDING

### Environment variables

Use the following environment variables to configure our containers.

#### The Postgres container

| Variable name | Example | Description | Notes |
| ------------- | ------- | ----------- | ----- |
| Postgres_DB   | stoplight | The initial Postgres database to create | Must match the value in the API's SL_Postgres_URL |
| Postgres_USER | stoplight | The initial Postgres user to create | Must match the value in the API's SL_Postgres_URL |
| Postgres_PASSWORD | stoplight | The password for Postgres_USER | |

#### The API container

| Variable name | Example | Description | Notes |
| ------------- | ------- | ----------- | ----- |
| SL_Postgres_URL   | Postgres://stoplight:stoplight@Postgres.example.com:5432/stoplight | Postgres username, password, URL, and database for the API to use | Must match the values configured for Postgres |
| SL_Postgres_SCHEMA | production | The Postgres schema to create and use |  |
| SL_APP_SECRET | cgL49eK8cigD8SOyLzaI | The secret seed used to create and verify tokens | |

#### The app container

| Variable name | Example | Description | Notes |
| ------------- | ------- | ----------- | ----- |
| SL_API_URL   | https://stoplight-api.example.com:443 | The URL used to reach the API from the browser client. | |

## Deploy on OpenShift

### Using Stoplight's deployment template

You can jumpstart your installation by taking advantage of our OpenShift Stoplight deployment template: PENDING

### Manual installation

1. Get a Docker login and password from Stoplight to access our private quay.io repository.
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

    > Note: as an alternative to creating image streams for the app and API, you can [manually select a version](#manually-select-an-app-and-API-version) and watch the Stoplight JSON manifest for changes.{{< temp >}}A note from Collin: NOTE: need to think more about what we want to recommend as the default{{< /temp >}}
5. Create secrets for the secret environment variables you need to pass to each service. Refer to the [general configuration guide](#environment-variables) for the names and values of these variables.
6. Create a persistent storage asset for Postgres. Refer to the [general configuration guide](#resource-requirements) for recommended sizing 
7. Create a deployment for Postgres, with the following settings:
    - Readiness probe: `/usr/libexec/check-container 5s delay, 1s timeout`
    - Liveliness probe: `/usr/libexec/check-container --live 120s delay, 10s timeout`
    - Mount: `$Postgres_data_volume → /var/lib/pgsql/data`
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
12. Navigate to the app endpoint. If all is well, you should see an initial installation page. Proceed with our [site admin initial configuration documentation](../site_admin/initial-configuration.md).

## Deploy on a Unix server

Install Docker and run these containers:

Links to the containers?

## Appendix

### Manually select an app and API version

PENDING

### docker-compose reference file

PENDING

### Omnibus image

PENDING

### Recommended setups when not using OpenShift

PENDING

Debs note to self: might move this to Unix server section, depending on contents

