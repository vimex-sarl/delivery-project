# Vimex Delivery - Public Build Repository

This is a public repository containing only build configurations and GitHub Actions workflows for the Vimex Delivery project.

## Purpose

- Contains GitHub Actions workflows for CI/CD
- Builds and deploys from the private repository (`vimex-sarl/delivery`)
- Maintains separation between public build configurations and private source code
- Provides secure deployment pipelines without exposing sensitive source code

## Repository Structure

```
public-repo-setup/
├── .github/
│   └── workflows/
│       ├── dev-deploy-cloud-functions.yml
│       ├── dev-deploy-firebase-hosting.yml
│       ├── prod-deploy-cloud-functions.yml
│       ├── prod-deploy-firebase-hosting.yml
├── scripts/
│   └── setup-ssh-key.sh
├── README.md
└── .gitignore
```

## Setup Instructions

### 1. Create the Public Repository

1. Go to GitHub and create a new repository named `delivery-project`
2. Make sure the repository is public
3. Do not initialize with README, license, or .gitignore

### 2. Push Initial Setup

```bash
# Clone the setup files
git clone <this-repo-url> public-repo-setup
cd public-repo-setup

# Initialize git and push to new public repository
git init
git add .
git commit -m "Initial setup: Public build repository"
git branch -M main
git remote add origin git@github.com:vimex-sarl/delivery-project.git
git push -u origin main
```

### 3. Configure GitHub Secrets

In the public repository (`delivery-project`), configure the following secrets:

**Required Secrets:**
- `PRIVATE_REPO_TOKEN`: Personal Access Token with `repo` scope for accessing the private repository
- All existing Firebase service account keys and API keys from the private repository

**Firebase Secrets (copy from private repo):**
- `FIREBASE_SERVICE_ACCOUNT_VIMEX_COURSIER_DEV`
- `FIREBASE_SERVICE_ACCOUNT_VIMEX_COURSIER_PROD`
- `FIREBASE_CONFIG_JSON_CUSTOMER_WEB_DEV`
- `FIREBASE_CONFIG_JSON_ADMIN_WEB_DEV`
- `FIREBASE_CONFIG_JSON_CUSTOMER_WEB_PROD`
- `FIREBASE_CONFIG_JSON_ADMIN_WEB_PROD`
- And all other Firebase-related secrets

**API Keys (copy from private repo):**
- `OPENAI_API_KEY_DEV`
- `STRIPE_SECRET_KEY_DEV`
- `STRIPE_WEBHOOK_SECRET_SELF_ACCOUNT_DEV`
- `STRIPE_WEBHOOK_SECRET_CONNECTED_ACCOUNT_DEV`
- And all other API keys

### 4. Create Personal Access Token for Private Repository Access

1. Go to GitHub → **Settings** → **Developer settings** → **Personal access tokens** → **Tokens (classic)**
2. Click **Generate new token** → **Generate new token (classic)**
3. Configure the token:
   - **Note**: `github-actions-public-repo`
   - **Expiration**: Set an appropriate expiration (recommended: 1 year)
   - **Scopes**: Select `repo` (full control of private repositories)
4. Click **Generate token**
5. Copy the generated token and add it as `PRIVATE_REPO_TOKEN` secret in the public repository

### 6. Link as Submodule in Private Repository

In the private repository (`delivery`):

```bash
git submodule add git@github.com:vimex-sarl/delivery-project.git public-build
git add .gitmodules public-build
git commit -m "Add public build repository as submodule"
git push
```

## Workflow Overview

### Development Deployments

- **Cloud Functions**: [`dev-deploy-cloud-functions.yml`](.github/workflows/dev-deploy-cloud-functions.yml)
- **Firebase Hosting**: [`dev-deploy-firebase-hosting.yml`](.github/workflows/dev-deploy-firebase-hosting.yml)

### Production Deployments

- **Cloud Functions**: [`prod-deploy-cloud-functions.yml`](.github/workflows/prod-deploy-cloud-functions.yml)
- **Firebase Hosting**: [`prod-deploy-firebase-hosting.yml`](.github/workflows/prod-deploy-firebase-hosting.yml)

## How It Works

1. **Private Repository Fetch**: All workflows start by cloning the private repository using SSH authentication
2. **Build Process**: The build process runs against the cloned private repository
3. **Deployment**: Deployment uses the same Firebase service accounts and configurations
4. **Tag Management**: Production workflows manage deployment tags to track changes

## Security Considerations

- No sensitive source code is stored in this repository
- Only build configurations and deployment scripts
- Private repository access is handled via GitHub Secrets and SSH deploy keys
- All API keys and service accounts remain secure in GitHub Secrets

## Maintenance

### Updating Workflows

When making changes to GitHub Actions:

1. Update the workflow files in this repository
2. Test the deployments from the public repository
3. The private repository will automatically use the latest workflows via the submodule

### Adding New Secrets

When new secrets are required in the private repository:

1. Add the secret to the private repository
2. Also add it to the public repository with the same name
3. Update the relevant workflow files to use the new secret

## Troubleshooting

### Common Issues

1. **SSH Key Authentication Fails**
   - Verify the SSH key is correctly added as a deploy key in the private repository
   - Check that the private key is correctly stored in `PRIVATE_REPO_SSH_KEY` secret

2. **Build Failures**
   - Verify all required secrets are configured in the public repository
   - Check that the private repository structure matches expected paths

3. **Deployment Failures**
   - Verify Firebase service account keys are valid
   - Check that the Firebase project IDs are correct

## Support

For issues with the build and deployment system, check:
- GitHub Actions logs in the public repository
- Verify all secrets are properly configured
- Ensure SSH key has correct permissions

---

**Note**: This repository should only contain build configurations. All source code remains in the private repository (`vimex-sarl/delivery`).