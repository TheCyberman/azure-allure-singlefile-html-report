# Allure Html Report 

An Azure DevOps extension that provides a task for publishing HTML formatted report form Allure --single-file generation into build and release pages.

## Using the extension

In order to see a report tab you must use the  `Publish Allure Single File HTML Report` task. This is a supporting task which adds HTML output from the build pipeline(s) for viewing.

- The task takes one mandatory parameter `reportDir` which should reference the HTML filename or directory to be published.

### Example YAML use

```YAML
steps:
  - task: PublishHtmlReport@1
    displayName: 'Allure Test Report'
    inputs:
      reportDir: '$(ResultsPath)/reportName.html'
      useFilenameTabs: true
      tabName: TestReport
```

## Limitations and known issues

### HTML file compatibility

Due to the way this extension works, we recommend that any HTML files published operate as entirely self-contained, single page reports. External links or style sheets that are embedded in HTML files may not function within the Azure DevOps UI due to CORS restrictions. It is also not possible to use relative links in HTML reports that are published via the extension since these will not resolve to the build artifacts in Azure DevOps. Anchor links should work as expected.

### Large HTML reports

Very large reports (e.g. greater than 50MB) may cause the task to exhaust the default Node memory allocation (as per this reported [issue](https://github.com/blakyaks/azure-pipeline-html-reports/issues/13)), leading to the task failing and not uploading the report as an attachment. In order to work around this issue it may be necessary to add an additional step in your pipelines before the publish task is executed:

```yaml
- powershell: |
    # Set the NODE_OPTIONS environment variable
    echo "##vso[task.setvariable variable=NODE_OPTIONS]--max-old-space-size=8192"
  displayName: 'Set NODE_OPTIONS Environment Variable'
```

Note that large reports will also take an extended time to load in the UI and should be avoided where possible by breaking down the reports into smaller units.

## Development

### Build Instructions

To build the extension locally, run the following commands:

```bash
npm run build && npm run extension:package && npm run extension:publish
```

This will compile the source code, package the extension, and create a VSIX file that can be installed in Azure DevOps.

