# FauxGraph Journal

## TODO

TODO:

- Why isn't Mgid set as container app identity? (cf, Configure/Identity in https://containerapps.azure.com/)
- Connect '/data' to Az Stg with tenant's data
- Fixed yet? [app id bug](#azurerm_container_app-identity-bug)

## History

### 08/25/2026

- Implemented basic health checks; KEDA was shutting down container due to HTTP 400s
  - Added Bruno tests for health checks
- Using [ACA portal](https://containerapps.azure.com)
  - Manually set ACA's scale to 1:1 - run only 1 and keep it running.
  - Manually set ACA's target port to 8080
- [19: Fix tenant lookup](https://github.com/FauxGraph/cohere/issues/19)
- Resolved [16: Enable Az health probes](https://github.com/FauxGraph/cohere/issues/16)

### 08/24/2026

- Enabled ACR admin user for direct GH integration in Container App/Settings/Deployment

- App Registration for GitHub OIDC integrationg
  - Lots of fumbling around, but got it work work
  - Switched from TF to AzCli for creating the App Reg and setting roles, etc.
- Fixed data path issues
  - Determine the absolute data path from (in order) COHERE_DATA_PATH, app.config's CohereDataPath, or '/data'.
  - If the absolute path exists, use it. If not, throw exception and exit.
  - Best practice: specific path in COHERE_DATA_PATH. This works for all execution contexts.

### 08/20/2026

- Recreate FauxGraph in Azure
  - Deleted RG w/all resources using portal
  - Recreated using
    - base-infra

#### azurerm_container_app identity bug

After _terraform plan_ for container-app, the managed identity has not been set properly (per the container portal). I had to manaully set the Managed Id.


### 08/19/2026

- Terraform
- Cohere
  - Set dir structure for both existing tenants
    - Dirs by entities allow for multiple copies of json files which can be merged / de-dup'd.
    - Dir structure is:

    ```shell
    <tenant-uuid>/
      azgraph/
        authorizationservices/
        resources/
        subscriptions/
      msgraph/
        applications/
        contacts/
        groups/
        managedIdentities/
        organizations/
        people/
        serviceprincipals/
        teams/
        users/
    ```

### 08/18/2026

- Got website [code](../fauxgraph-cf/) working well enough & prop'd to Cloudflare.
- Using jburnett@altamodatech.com as Work acct in Azure portal, created:
  - subscription, az-fauxgraph-p-1 (id: d617d7ef-3a4f-494f-9bf7-97e1fe180a26)
    - RG, az-ue2-fauxgraph-rg-p-1
      - Azure Container 
      - Container App Env, az-ue2-fg-cenv-p-1
        - Zone redundancy: no
        - Monitoring:
          - LAW, az-ue2-fg-law-p-1
        - Networking:
          - Public access: allow
        - Container App, az-ue2-fg-cohere-capp-p-1
          - Deployment source: image
          - use Azure Container Registry, azue2fgcregp1
            - Domain label scope: Subscription Reuse
            - SKU: Basic
            - Registry FQDN (Az set): azue2fgcregp1-gegzhhg0fuf7g9c2.azurecr.io (global endpoint)
            - Avail zones: no
            - Permissions mode: RBAC
- Created FauxGraph/hosting repo for terraform
  - Created Terraform for:
    - Az Container App Env, azure/container-env
    - Az Container App, azure/container-app
  