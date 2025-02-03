<a id="readme-top"></a>

<!-- PROJECT LOGO -->
<br />
<div align="center">
  <a href="https://github.com/GribouilleVert/sftp-deployments">
    <img src=".github/assets/logo.png" alt="Logo" width="80" height="80">
  </a>

  <h3 align="center">GribouilleVert/sftp-deployments</h3>

  <p align="center">
    A GitHub action to make SFTP deployments with no downtimes 🚀
    <br />
    <br />
    <a href="https://github.com/GribouilleVert/sftp-deployments/issues/new?labels=bug">Report Bug</a>
    ·
    <a href="https://github.com/GribouilleVert/sftp-deployments/issues/new?labels=enhancement">Request Modification</a>
  </p>
</div>

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
    </li>
    <li>
      <a href="#usage">Usage</a>
      <ul>
        <li><a href="#usage_prerequisites">Prerequisites</a></li>
      </ul>
    </li>
    <li><a href="#action-inputs">Action Input</a></li>
    <li><a href="#how-it-works">How It Works</a></li>
    <li><a href="#requirements">Requirements</a></li>
    <li><a href="#troubleshooting">Troubleshooting</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
  </ol>
</details>

<!-- ABOUT THE PROJECT -->

## About The Project

This GitHub Action deploys your code to an SFTP server with minimal downtime by creating versioned directories and updating a `current symlink to point to the latest release. It also supports preserving certain files or folders in a permanent directory.


<!-- USAGE -->

## Usage

This is an example of how you may give instructions on setting up your project locally.
To get a local copy up and running follow these simple example steps.

### <span id="usage_prerequisites">Prerequisites</span>

To use this action in your repository, create or update a workflow (e.g., `.github/workflows/deploy.yml`) with the following content:

```yaml
name: SFTP Deployment

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Check out repository
        uses: actions/checkout@v3

      - name: Deploy to SFTP
        uses: GribouilleVert/sftp-deployments@main
        with:
          host: ${{ secrets.SFTP_HOST }}
          port: 22
          user: ${{ secrets.SFTP_USER }}
          private_key: ${{ secrets.SFTP_KEY }}
          local_path: './dist'
          remote_path: '/var/www/my-app'
          keep: '[".env", "uploads"]'
          post_deploy_commands: |
            npm run build
```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ACTIONS INPUT -->

## Action Inputs

| Input                    | Description                                                                                                                                                                                                                                     | Type   | Required | Default |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|----------|---------|
| **host**                 | The server host or IP                                                                                                                                                                                                                           | string | true     | None    |
| **port**                 | The server port                                                                                                                                                                                                                                 | number | false    | `22`    |
| **user**                 | The SSH user to use                                                                                                                                                                                                                             | string | true     | None    |
| **private_key**          | The SSH private key used for authentication                                                                                                                                                                                                     | string | true     | None    |
| **local_path**           | The local path to deploy                                                                                                                                                                                                                        | string | false    | `./`    |
| **remote_path**          | The remote path in which to deploy                                                                                                                                                                                                              | string | true     | None    |
| **keep**                 | A JSON array of file or folder names to preserve in a permanent directory. These files/folders must exist under `permanent/` on the remote. They will be symlinked to each new version.                                                         | string | false    | `[]`    |
| **post_deploy_commands** | Commands to run on the server following after the files have been copied and the links to `permanents/` files have been created, but before the deployment is promoted to current. The default directory is the deployment's version directory. | string | false    | `[]`    |

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- HOW IT WORKS -->

## How It Works

1. **Versioned Deployments**
    The action creates a new version folder based on the current timestamp (e.g., `2025-02-03_12-34`) inside a `versions` directory on the server.

2. **Permanent Storage**
    Files or folders listed in `keep` are assumed to reside in a `permanent` directory on the server. During each deployment, the action creates symlinks from the new version to the content in the `permanent` folder.
    - For example, if `keep` is `[".env"]`, you should have a `.env` file under `permanent/.env` on the server. This file won’t be overwritten by subsequent deployments.

3. **Symlink Update**
    Once the new version is uploaded, the `current` symlink is updated to point to the new version, ensuring minimal downtime.

4. **Cleanup of Old Releases**
    The action removes older versions beyond the last 5 to avoid accumulating too many stale deployments.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- REQUIREMENTS -->

## Requirements

- A GitHub Runner with SSH capabilities.
- An SSH key (`private_key` input) that has write access to the remote server.
- Proper permissions on the remote server to create folders, upload files, and create symlinks.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- TROUBLESHOOTING -->

## Troubleshooting

- **Permissions Issues:** Check the SSH key permissions and ensure the remote user can write to `remote_path`.
- **Missing Permanent Files:** If `keep` lists files/folders that do not exist under `permanent/`, a warning will be displayed. Ensure they are pre-created or removed from `keep`.
- **File Already Exists:** If a file or symlink of the same name already exists in the new version directory, remove it or rename it on the server before deploying again.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- TROUBLESHOOTING -->

## License

This project is licensed under the [MIT License](./LICENSE).

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTACT -->

## Contact

-   Vasco Compain - [@GribouilleVert](https://github.com/Kylian-Mallet) - [vert@gribouille.io](mailto:vert@gribouille.io)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

Happy deploying! If you have any questions or run into issues, please open a GitHub issue or submit a pull request.
