# Deploy site to Hostinger

This action deploys your build folder to a live domain in [Hostinger](https://www.hostinger.com). It uploads your output directory to your domain's `public_html` folder.

Note: there's no need to push your dist files to the repository.

### Hostinger SSH Access
In order to use this action you must [enable SSH Access](https://support.hostinger.com/en/articles/1583645-how-to-enable-ssh-access) in your Hostinger panel. Then, set the credentials as [repository secrets](https://docs.github.com/en/actions/security-for-github-actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-a-repository).

- `HOSTINGER_HOST`
- `HOSTINGER_PASSWORD`
- `HOSTINGER_USERNAME`
- `HOSTINGER_PORT`

## Usage

### Inputs

- `domain` - Required: Your domain registered in Hostinger.
- `source-path` - Required: the location of your built project inside the repository (out, dist, etc).

### Example workflow:

#### Build and Deploy to Hostinger

Create a file at `.github/workflows/deploy.yml` in your site's repository with the following content.

```yml
name: Build and deploy to Hostinger

on:
  # Trigger the workflow every time you push to the `main` branch
  # Using a different branch name? Replace `main` with your branch’s name
  push:
    branches: [main]
  # Allows you to run this workflow manually from the Actions tab on GitHub.
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Install dependencies
        run: npm install

      - name: Build project
        run: npm run build # The build command of your project that generates out/, dist/, or a custom directory

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Hostinger
        uses: mradilla/hostinger-deploy@v1
        with:
            domain: example.com # Your domain in Hostinger (required)
            source-path: dist # Path of the directory containing your site (out, dist, build) (required)
```