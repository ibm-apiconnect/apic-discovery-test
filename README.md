# apic-discovery-test

The Apic Discovery Test repo allows you to test the discovery action from [here](https://github.com/ibm-apiconnect/apic-discovery-action). This is an example repo from where OpenAPI reference documents and MCP (Model Context Protocol) servers can be sent to the APIConnect manager using the discovery action and where they can be promoted as required to be managed by APIConnect through their entire lifecycle.

See [discover-api.yml](.github/workflows/discover-api.yml) and [send-to-discovery.yml](.github/workflows/send-to-discovery.yml).

The file `discover-api.yml` has an example of sending API documents in different ways to the host `d-j02.apiconnect.dev.automation.ibm.com` to the provider organisation name `niraimathi` when the [workflow file](.github/workflows/discover-api.yml) or any one of the mentioned target files changes.<br /> 

The job `check_changes_job` checks for the changes in the workflow file or target documents and the job `run-discovery` sends the documents mentioned.<br /> 
The `run-discovery` job uses the apic-discovery-action repo main branch in the ibm-apiconnect organization:
```yaml
- uses: ibm-apiconnect/apic-discovery-action@main
  with:
    api_host: ${{ env.API_HOST }}
    platform_api_prefix: ${{ env.PLATFORM_API_PREFIX }}
    provider_org: ${{ env.PROVIDER_ORG }}
    api_key: ${{ secrets.apicApikey }}
    api_files: ${{ env.API_FILES }}
    api_folders: ${{ env.API_FOLDERS }}
    mcp_files: ${{ env.MCP_FILES }}
    mcp_folders: ${{ env.MCP_FOLDERS }}
    insecure_skip_tls_verify: ${{ env.INSECURE_SKIP_TLS_VERIFY }}
    resync_check: ${{ needs.check_changes_job.outputs.action_changed && true || false }}
```

The file `send-to-discovery.yml` is another example of sending documents with the extra variable **`git_diff`** which sends the git diff files between the current and previous commit. And the discovery action will verify whether any of the changed files include files supplied in `api_files`, `api_folders`, `mcp_files`, or `mcp_folders`.

The content can be modified according to the test requirement for sending APIs and MCP servers.

Note: When an API with a large payload (complex API more than 1 MB in size) is sent, there is expected to be some delay in creating the discovered API. Similarly, processing MCP server records as discovered assets may take a brief moment. It is recommended not to run another workflow simultaneously until you see the discovered API or discovered asset has been created in the discovery service.
