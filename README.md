# Azure Pipeline for Uploading Files to Azure Blob Storage

This Azure Pipeline script is designed to to install and publish npm packages or to run an `npm` command. Supports `npmjs.com` and authenticated registries like Azure Artifacts.

## Parameters

| Parameter     | Type   |   Default | Values | Optional/Required | Description       |
|---------------|--------|-----------|--------|-------------------|-------------------|
| `command`     |string| 'install'| 'ci' / 'install' / 'publish' /'custom' | Required | Specifies the command and arguments, which are passed to npm for execution.If your arguments contain double quotes ("), escape them with a slash (\), and surround the escaped string with double quotes (") |
| `workingDir`      | string | $(System.DefaultWorkingDirectory) | | Optional  | Working folder that contains package.json, Specifies the path to the folder containing the target package.json and .npmrc files. Select the folder, not the file.|
| `verbose`         | boolean | false | true / false | Optional | Use when command = install / command = ci / command = publish.Prints more information to the console when the task runs |
| `customCommand`    | string | | | Optional  | Runs a custom command. Example: dist-tag ls mypackage.|
| `customRegistry` | string  | useNpmrc | useNpmrc/useFeed | Optional  | Define the registry to use either the one in npmrc file or the feed defined.Use when command = install / command = ci / command = custom|
| `customFeed`      | string |  |  | Optional  | Use packages from this Azure Artifacts/TFS registry. Required when customRegistry = useFeed && command = install / command = ci / command = custom. Includes the selected feed in the generated .npmrc. For project-scoped feeds, use ProjectName/FeedName or ProjectID/FeedID. For organization-scoped feeds, the value should be the feed name.|
| `customEndpoint`| string | | |Optional |Credentials for registries outside this organization/collection Use when customRegistry = useNpmrc && command = install / command = ci / command = custom. Credentials to use for external registries located in the project's .npmrc. Leave this blank for registries in this account/collection; the task uses the build's credentials automatically.|
| `publishRegistry`| string | useExternalRegistry | useExternalRegistry/useFeed| Optional | Specifies the registry that the command will target.Use when command = publish. Allowed values: useExternalRegistry (External npm registry (including other accounts/collections)), useFeed (Registry I select here). |
| `publishFeed`      | string |  |  | Optional  |Specifies a registry hosted in the account. You must have Package Management installed and licensed to select a registry here. Required when publishRegistry = useFeed && command = publish.|
| `publishPackageMetadata`| boolean |true |true/false |Optional | Publish pipeline metadata.Associates the build/release pipeline's metadata (the run # and source code information) with the package.Use when command = publish && publishRegistry = useFeed && command = install / command = ci / command = publish. |
| `publishEndpoint`| string |  | | Optional | Required when publishRegistry = useExternalRegistry && command = publish. Specifies the credentials to use for publishing to an external registry.|

## Use case Example

You need to have a service connection related to GitHub on the Azure DevOps project.
To call this in your pipeline you can follow this example:

   ```yaml
  # azure-pipeline.yml
  resources:
    repositories:
      - repository: Template
        type: github
        name: NashTech-Labs/AzurePipelineNpmPackages
        ref: main
        endpoint: '<GitHubServiceConnection>'

  steps:

  - template: npmPackage.yml@Template
    parameters:
      command: ci
      workingDir: $(Build.SourcesDirectory)
  ```

You can modify the options to use as per the use case.
