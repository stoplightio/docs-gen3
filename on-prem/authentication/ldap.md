# LDAP for Active Directory

These instructions assume you have already configured your LDAP server.

Follow these steps to create a new LDAP service:

1. Select **Add Provider**.
2. Provide the details of the new service, including:
    1. **Name**: name for this provider in Stoplight.
    2. **URL**: hostname and port number of the LDAP server.
    3. **Bind DN**: distinguished name of the bind LDAP user.
    4. **Bind credentials**: password of the bind LDAP user.
    5. **Search base**: starting location within the LDAP directory for performing user searches.
3. Select **Save**.

This table provides more information on the LDAP authentication parameters:

| Field | Description|
| ----- | ---------- |
| **Name** | The name for the authentication service in Stoplight. This can be any value you choose. You use it to identify the service in the Stoplight dashboard. |
| **URL** | The hostname and port number for your LDAP server. This is the host's fully qualifies domain name (FQDN). |
| **Bind DN** | The distinguished name (DN) of an LDAP user. This user must have search privileges on the LDAP directory. |
| **Bind credentials** | The password of the user specified in the Bind DN field. |
| **Search base** | The starting location within the LDAP directory for performing user searches. Also known as the User Base DN. Select the most specific location possible to improve performance. |
