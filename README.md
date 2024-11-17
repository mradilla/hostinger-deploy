# Deploy site to Hostinger

This GitHub Action simplifies deploying your built site to a live domain hosted on [Hostinger](https://www.hostinger.com). It uploads the contents of your output directory to your domain's `public_html` folder using [SCP Command GitHub Action](https://github.com/marketplace/actions/scp-command-to-transfer-files) for secure SSH file transfers.


Note: There's no need to push your dist files to the repository. This action handles the deployment automatically.

## Prerequisites

### 1. Enable SSH Access
To use this action, you must [enable SSH Access](https://support.hostinger.com/en/articles/1583645-how-to-enable-ssh-access) in your Hostinger panel.

### 2. Set Repository Secrets
Add the following secrets in your GitHub repository under **Settings > Secrets and Variables > Actions**. These secrets will need to be passed as inputs to the action (see the `Usage` section for an example):

- **`HOSTINGER_HOST`**: Your Hostinger server's hostname or IP address (e.g., `192.168.1.1`).
- **`HOSTINGER_PORT`**: Your Hostinger server's port (e.g., `1000`).
- **`HOSTINGER_USERNAME`**: Your SSH username (e.g., `u101`).
- **`HOSTINGER_PASSWORD`**: The password for your SSH user.

## Inputs


### Inputs
| Input           | Required | Description                                                         |
|-----------------|----------|---------------------------------------------------------------------|
| `domain`        | Yes      | Your domain name registered in Hostinger.                            |
| `source-path`   | Yes      | The directory containing your built site (e.g., `dist`).             |
| `host`          | Yes      | Use `${{ secrets.HOSTINGER_HOST }}`.                                 |
| `port`          | Yes      | Use `${{ secrets.HOSTINGER_PORT }}`.                                 |
| `username`      | Yes      | Use `${{ secrets.HOSTINGER_USERNAME }}`.                              |
| `password`      | Yes      | Use `${{ secrets.HOSTINGER_PASSWORD }}`.                              |

## Usage

### Example Workflow: Build and Deploy to Hostinger

Create a file at `.github/workflows/deploy.yml` in your site's repository with the following content:


```yaml
name: Build and deploy to Hostinger

on:
  # Trigger the workflow every time you push to the `main` branch
  # Using a different branch? Replace `main` with your branch’s name
  push:
    branches: [main]
  # Enable manual triggering from the GitHub Actions tab
  workflow_dispatch:

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Install dependencies
        run: npm install

      - name: Build project
        run: npm run build # Replace with your project's build command

      - name: Upload files to Hostinger
        uses: mradilla/hostinger-deploy@v1
        with:
          domain: example.com    # Replace with your Hostinger domain
          source-path: dist      # Replace with your build directory (e.g., out, dist, build)
          host: ${{ secrets.HOSTINGER_HOST }}
          port: ${{ secrets.HOSTINGER_PORT }}
          username: ${{ secrets.HOSTINGER_USERNAME }}
          password: ${{ secrets.HOSTINGER_PASSWORD }}
```

## Notes & Troubleshooting

- **Verify SSH Access**: Ensure that SSH access is correctly enabled in Hostinger, and your credentials are valid. Test your connection with an SSH client (e.g., `ssh username@host -p port`).

- **Directory Permissions**: The action assumes you have permissions to write to the `public_html` folder. Contact Hostinger support if deployment fails due to permission issues.
- **Custom Directories**: If your project uses a custom output folder (not `dist` or `out`), update the `source-path` input accordingly.
- **Debugging**: Use the **Actions tab** in GitHub to view detailed logs in case of errors.