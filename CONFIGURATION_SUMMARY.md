# Mordor Faucet Configuration Summary

## Overview

This repository has been successfully configured for the Mordor Testnet (Ethereum Classic testnet). The configuration follows a dual-branch strategy to maintain synchronization with the upstream PoWFaucet repository while supporting Mordor-specific customizations.

## What Was Configured

### 1. Branch Structure

#### Main Branch (`main` or `master`)
- **Purpose**: Stays synchronized with upstream [pk910/PoWFaucet](https://github.com/pk910/PoWFaucet)
- **Protection**: Should be protected to only allow pulls from upstream
- **No Direct Changes**: All Mordor-specific changes go to the `mordor` branch

#### Mordor Branch (`mordor`)
- **Purpose**: Contains Mordor Testnet configuration and customizations
- **Configuration**: Pre-configured for Mordor Testnet
- **Deployment**: This is the branch to use for deployment

### 2. Mordor Testnet Configuration

Created `faucet-config.mordor.yaml` with the following settings:

- **Chain ID**: 63 (Mordor Testnet)
- **Network Name**: "Mordor Testnet"
- **Ticker**: mETC
- **RPC Endpoint**: https://rpc.mordor.etccooperative.org
- **Block Explorer**: https://explorer.mordor.etccooperative.org
- **Transaction Type**: Legacy (pre-EIP1559) transactions
- **Chain Variable Name**: mordor

### 3. CI/CD Workflows

Updated and created the following GitHub Actions workflows:

#### Updated Workflows
1. **test-build.yml**
   - Now runs tests for PRs to both `master` and `mordor` branches
   - Ensures code quality before merging

2. **build-latest-v2.yml**
   - Builds binaries when pushing to `master` or `mordor` branches
   - Creates releases for both branches

#### New Workflows
3. **mordor-docker.yml**
   - Builds Docker images specifically for Mordor
   - Tags images as `mordor-latest`, `mordor-vX.X.X`
   - Pushes to `mordortestnet/faucet` Docker Hub repository
   - Supports multi-architecture builds (amd64, arm64)

### 4. Documentation

Created comprehensive documentation:

1. **BRANCH_STRATEGY.md**
   - Explains the dual-branch strategy
   - Documents workflow for syncing with upstream
   - Provides guidelines for making Mordor-specific changes
   - Includes branch protection recommendations

2. **DEPLOYMENT.md**
   - Complete deployment guide for Mordor faucet
   - Multiple deployment options (Node.js, Docker, systemd)
   - Security best practices
   - Configuration details
   - Troubleshooting guide

3. **Updated README.md**
   - Added Mordor Testnet section at the top
   - Added Mordor instance to the instances table
   - Links to branch strategy documentation

## Configuration Details

### Mordor-Specific Settings

The Mordor configuration includes several ETC-specific adjustments:

1. **Legacy Transactions**: Enabled (`ethLegacyTx: true`) as Mordor doesn't support EIP-1559
2. **ENS Module**: Disabled as Mordor doesn't have ENS
3. **Network Naming**: Updated all references from ETH to ETC/mETC
4. **Explorer Links**: Point to Mordor block explorer

### Protection Modules

All protection modules from upstream are available:
- Captcha (HCaptcha/ReCAPTCHA/Turnstile)
- Proof of Work (Mining)
- IP-based restrictions
- Balance checks
- Recurring limits
- Concurrency limits
- GitHub authentication (optional)
- Gitcoin Passport (optional)

## Next Steps for Administrators

### 1. Branch Protection Rules

Set up branch protection for both branches in GitHub Settings:

#### For `main` branch:
```
☑ Require pull request reviews before merging
☑ Require status checks to pass before merging
☑ Require branches to be up to date before merging
☑ Do not allow bypassing the above settings
```

#### For `mordor` branch:
```
☑ Require status checks to pass before merging
☐ Require pull request reviews (optional, but recommended)
☑ Allow administrators to bypass (for quick config updates)
```

### 2. Secrets Configuration

Add the following secrets to GitHub repository settings for CI/CD:

- `DOCKERHUB_USERNAME`: Docker Hub username
- `DOCKERHUB_TOKEN`: Docker Hub access token
- `CODECOV_TOKEN`: CodeCov token (optional)

### 3. Deploy the Faucet

Follow the instructions in [DEPLOYMENT.md](DEPLOYMENT.md) to deploy:

1. Clone the repository and checkout `mordor` branch
2. Configure your secrets (wallet key, captcha keys)
3. Choose deployment method (Docker recommended)
4. Deploy and test

### 4. Update Configuration

To customize the faucet for your needs:

1. Copy `faucet-config.mordor.yaml` to `faucet-config.yaml`
2. Update the following required settings:
   - `faucetSecret`: Generate a random secret string
   - `ethWalletKey`: Your faucet wallet private key
   - `captcha.siteKey`: Your captcha site key
   - `captcha.secret`: Your captcha secret key
3. Optionally adjust:
   - Drop amounts (`maxDropAmount`, `minDropAmount`)
   - Protection module settings
   - Session timeouts
   - Outflow limits

## Testing

All tests pass successfully:
- ✅ 317 tests passing
- ✅ Build succeeds
- ✅ Configuration file validates correctly

## Maintenance

### Syncing with Upstream

To pull updates from upstream PoWFaucet:

1. Update `main` branch from upstream
2. Merge `main` into `mordor`
3. Resolve any conflicts (prefer Mordor-specific settings)
4. Test thoroughly
5. Deploy

### Making Mordor Changes

For Mordor-specific changes:

1. Create feature branch from `mordor`
2. Make changes
3. Create PR to `mordor` branch
4. Wait for CI to pass
5. Merge and deploy

## Support Resources

- **Upstream Project**: https://github.com/pk910/PoWFaucet
- **Documentation**: https://github.com/pk910/PoWFaucet/wiki
- **Mordor Testnet**: https://github.com/eth-classic/mordor
- **ETC Discord**: https://ethereumclassic.org/discord

## Files Added/Modified

### Added Files
- `faucet-config.mordor.yaml` - Mordor configuration
- `BRANCH_STRATEGY.md` - Branch strategy documentation
- `DEPLOYMENT.md` - Deployment guide
- `.github/workflows/mordor-docker.yml` - Mordor Docker workflow
- `CONFIGURATION_SUMMARY.md` - This file

### Modified Files
- `README.md` - Added Mordor section and instance
- `.github/workflows/test-build.yml` - Added mordor branch
- `.github/workflows/build-latest-v2.yml` - Added mordor branch

## Version Information

- **Server Version**: 2.4.2
- **Configuration Version**: 2
- **Mordor Chain ID**: 63
- **Upstream**: pk910/PoWFaucet

## Security Considerations

⚠️ **Important Security Notes**:

1. Never commit actual wallet private keys to the repository
2. Use environment variables or secrets management for sensitive data
3. Regularly update dependencies for security patches
4. Monitor faucet balance and set up alerts
5. Use HTTPS in production with valid SSL certificates
6. Implement rate limiting at the reverse proxy level
7. Keep logs for security audit purposes

## Conclusion

The Mordor Testnet faucet is now fully configured and ready for deployment. The dual-branch strategy ensures that updates from upstream can be easily integrated while maintaining Mordor-specific customizations. Follow the deployment guide to get started, and refer to the branch strategy document for ongoing maintenance.

For questions or issues, please open an issue in this repository or reach out to the ETC community.
