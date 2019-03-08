# Pipelines

Azure Pipeline Templates

## Usage

To use the templates your `azure-pipelines.yml` must import this repo like:

```yml
resources:
  repositories:
    - repository: templates
      type: github
      name: dirigeants/pipelines
      endpoint: dirigeants # This is likely your github username or organization name
```

**Note:** You will have to allow your pipeline to use your github credentials to be used to fetch templates from other repos.
You can do this by going to your Project Settings (in azure devops):
1. Pipelines
1. Service Connections 
1. (your username/orgname should be listed, otherwise the endpoint will be whatever is listed) 
1. Policies 
1. Check: `Allow all pipelines to use this endpoint` 
1. Save Changes

### Lint Template

This template will install the specified version of node.js, install dependencies, and run the `test:lint` script defined in your package.json. (meaning you can use any lint tool or any combination of tools)

```yml
jobs:
  - template: lint.yml@templates
    parameters: # Optional if you want to use all defaults
      versionSpec: '10.x' # Optional (default: '10.x'): The version of node.js you want to use, valid values are ['8.x', '9.x', '10.x', '11.x', ...]
```

### Docs Template

This is a build and commit to github template, so it can be used for more than just documentation, however it's general usecase is for documenation.

Your build must be configured to output files to the `/dist` folder of your repo. Additionally you must have two secret environmental variables set in your project. As a convience you can create these under your libraries so that you can link them to other pipelines in your project. These varibales are `ghToken`, a github token with the `public_repo` permission from your account; and `commitEmail`, the email address to use as the user who is commiting the changes. Please ensure you do use the "secret variable" setting, otherwise these values will be leaked in the logs.

```yml
jobs:
  - template: docs.yml@templates
    parameters:
      ghRef: '' # Required: The repo to commit to such as 'dirigeants/pipelines`
      targetBranch: 'gh-pages' # Optional: The name of the branch to commit the build to
      masterOnly: 'false' # Optional: will only commit the build for master branch if this is set to 'true'
      type: 'Docs' # Optional: the name of the job, and is also used in the commit message
      command: 'docs' # Optional: the name of the npm script to build your project
      dependsOn: '' # Optional: A list of other jobs that must be successful before this one runs
      versionSpec: '10.x' # Optional: The version of node.js you want to use, valid values are ['8.x', '9.x', '10.x', '11.x', ...]
```
