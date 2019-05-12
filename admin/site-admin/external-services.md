## Integrate external services

Stoplight allows you to integrate external services, such as remote code repositories, into your Stoplight organization. Stoplight supports the following integrations:

- Bitbucket
- GitHub
- GitLab

Access **External Services** from the Stoplight dashboard by selecting:

**admin** > **Site Admin** > **External Services**

## Add a new service

When you add a version control service, project, repositories and users automatically sync.

The following mapping applies:

- Bitbucket: a Bitbucket Project maps to a Stoplight Organization, while a single Bitbucket repo maps to a Stoplight Project.
- GitHub:
- GitLab: <!-- TODO: add mapping info when available -->

<!-- QUERY: does the external repo need to have certain files/configs/setup? -->

1. Select **Add Service**. This opens a modal.
2. Provide the details of version control service connection, including:
    1. **Provider**: this can be any of the supported integrations.
    2. **Name**: choose a name to identify this service in the Stoplight interface.
    3. **API URL**: 
    4. **Username**: your username for the service.
    5. **Password**: your password for the service.
3. Select **SAVE**. Stoplight returns you to the **External Services** list. You can view and edit your new integration.

## Edit a service

You can edit all the service settings apart from the provider.

1. Select **Edit**.
2. Make any changes.
3. Select **SAVE**.

## Delete a service

1. Select **Delete service** <!--TODO: button icon here -->.
2. Complete the prompt to confirm you want to delete the service.

Service deletion is irreversible.