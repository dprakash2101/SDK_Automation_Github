# Generate SDK and Push to Another Repository - GitHub Workflow

## Overview

This GitHub Actions workflow is designed to automate generating a software development kit (SDK) using the OpenAPI Generator and pushing the generated SDK to another repository. The workflow triggers on push to the main branch of the repository.

## Workflow Steps

### 1. Checkout Repository

This step checks out the repository to the runner machine to provide access to the necessary files and configurations.

```yaml
- name: Checkout Repository
  uses: actions/checkout@v2
```

### 2. Install OpenAPI Generator

This step installs the OpenAPI Generator command-line interface (CLI) to generate the SDK based on the OpenAPI specification. You do not need to install Node.Js as it is already installed in the machine provided by GitHub.

```yaml
- name: Install OpenAPI Generator
  run: |
    npm install -g @openapitools/openapi-generator-cli@latest
```

### 3. Generate C# SDK

This step utilizes the OpenAPI Generator CLI to generate the C# SDK based on the provided OpenAPI specification (`openapispec.yaml`). Change the "openapispec.yaml" with your OpenAPI spec file. Adjust the generator and package name as needed. 

```yaml
- name: Generate C# SDK
  run: |
    openapi-generator-cli generate -i openapispec.yaml -g csharp -o ${{ github.workspace }}/csharpsdk --package-name CsharpSDK
```
You can generate SDK for different languages just by changing the language in the command and changing the package name as per your choice, for example we will do this with Python:
```yaml
- name: Generate C# SDK
  run: |
    openapi-generator-cli generate -i openapispec.yaml -g python -o ${{ github.workspace }}/pythonsdk --package-name PythonSDK
```


### 4. Configure Git for C# SDK

Configure Git with the user email and name for the C# SDK repository. Replace ”username.github.com” with the email associated with your GitHub account and “username” with your GitHub username.

```yaml
- name: Configure Git for C# SDK
  run: |
    git config --local user.email "username@github.com"
    git config --local user.name "username"
```

### 5. Push C# SDK to Another Repository

This step uses a custom GitHub Action (`cpina/github-action-push-to-another-repository`) to push the generated C# SDK to another repository. Replace placeholders with your specific information.

```yaml
- name: Push C# SDK to Another Repository
  uses: cpina/github-action-push-to-another-repository@main
  env:
    API_TOKEN_GITHUB: ${{ secrets.SDK_TOKEN }}
  with:
    source-directory: 'csharpsdk'
    destination-github-username: 'username'
    destination-repository-name: 'Reponame'
    user-email: 'username@github.com'
    target-branch: 'main'
```


## Configuration

### Personal access token generation
Account settings -> Developer Settings -> Personal access tokens -> Tokens (classic) -> Generate New Token 

The above process is for the generation of Personal access tokens.
### SDK Token

To use this workflow, you must have a Personal Access Token (PAT) with the repo scope. Generate a Personal Acces Token(PAT) with repo scope and copy it it will only be visible once Copy the token, which will be accessible only once. The steps to generate a Personal Access Token(PAT) are mentioned above.

Then, go to the repository's settings, navigate to "Settings -> Secrets and Variables -> Action -> Repository token," and save it as SDK_TOKEN.



## Notes

- Make sure to replace placeholders (e.g., `username`, `Reponame`) with your actual information.
- This workflow assumes the existence of an OpenAPI specification file in the root of the repository. Change the name of the “openapispec.yml” to the name of your OpenAPI spec file in the repository.
- Customize the OpenAPI Generator parameters in the "Generate C# SDK" step based on your preferences.
- Ensure the custom GitHub Action (cpina/github-action-push-to-another-repository) is accessible and compatible with your workflow.

Feel free to modify this workflow according to your specific requirements and preferences.


## Sample Workflow

```yml

name: Generate SDK and push it to Another Repository

on:
  push:
    branches:
      - main

jobs:
  push_to_another_repo:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v2

      - name: Install OpenAPI Generator
        run: |
          npm install -g @openapitools/openapi-generator-cli@latest
      - name: Generate C# SDK
        run: |
          openapi-generator-cli generate -i openapispec.yaml -g csharp -o ${{ github.workspace }}/csharpsdk --package-name CsharpSDK
      - name: Configure Git for C# SDK
        run: |
          git config --local user.email "username@github.com"
          git config --local user.name "username"
      - name: Push C# SDK to Another Repository
        uses: cpina/github-action-push-to-another-repository@main
        env:
          API_TOKEN_GITHUB: ${{ secrets.SDK_TOKEN }}
        with:
          source-directory: 'csharpsdk'
          destination-github-username: 'username'
          destination-repository-name: 'Reponame'
          user-email: 'username@github.com'
          target-branch: 'main'
```

## Author

- [Devi Prakash](https://github.com/dprakash2101)
