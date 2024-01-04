# Generate SDK and Push to Another Repository Workflow

## Overview

This GitHub Actions workflow is designed to automate the process of generating a software development kit (SDK) using the OpenAPI Generator and pushing the generated SDK to another repository. The workflow triggers on each push to the `main` branch of the repository.

## Workflow Steps

### 1. Checkout Repository

This step checks out the repository to the runner machine to provide access to the necessary files and configurations.

```yaml
- name: Checkout Repository
  uses: actions/checkout@v2
```

### 2. Install OpenAPI Generator

This step installs the OpenAPI Generator command-line interface (CLI) to generate the SDK based on the OpenAPI specification.

```yaml
- name: Install OpenAPI Generator
  run: |
    npm install -g @openapitools/openapi-generator-cli@latest
```

### 3. Generate C# SDK

This step utilizes the OpenAPI Generator CLI to generate the C# SDK based on the provided OpenAPI specification (`openapispec.yaml`). Change the "openapispec.yaml" with your openapispecfile. Adjust the generator and package name as needed. 

```yaml
- name: Generate C# SDK
  run: |
    openapi-generator-cli generate -i openapispec.yaml -g csharp -o ${{ github.workspace }}/csharpsdk --package-name CsharpSDK
```
You can generated SDK for different language just by changing language in the command and change the package name as per your choice, for example we will do with Python:
```yaml
- name: Generate C# SDK
  run: |
    openapi-generator-cli generate -i openapispec.yaml -g python -o ${{ github.workspace }}/cpythonsdk --package-name PythonSDK
```


### 4. Configure Git for C# SDK

Configure Git with the user email and name for the C# SDK repository.

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

Generate a Personal Acces Token with repo scope and copy it it will only visible once and got to settings of the repository where worflow is present and Settings -> Secrets and Variables -> Actions -> Repository secret and save it with SDK_TOKEN name or name of choice.
If you chose different name replace same in the workflow file.

## Configuration

### SDK Token

To use this workflow, you must have a Personal Access Token (PAT) with the `repo` scope. Copy the token, which will be accessible only once. Then, go to the repository's settings, navigate to "Settings -> Secrets and Variables -> Action -> Repository token," and save it as `SDK_TOKEN`.

## Notes

- Make sure to replace placeholders (e.g., `username`, `Reponame`) with your actual information.
- This workflow assumes the existence of an OpenAPI specification file named `openapispec.yaml` in the root of the repository.
- Customize the OpenAPI Generator parameters in the "Generate C# SDK" step based on your preferences.
- Ensure that the custom GitHub Action (`cpina/github-action-push-to-another-repository`) is accessible and compatible with your workflow.

Feel free to modify this workflow according to your specific requirements and preferences.

## Author

- [Devi Prakash](https://github.com/dprakash2101)
