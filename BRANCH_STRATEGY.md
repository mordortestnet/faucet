# Branch Strategy for Mordor Testnet Faucet

This repository maintains two primary branches with different purposes:

## Main Branch (`main` or `master`)
- **Purpose**: Stays synchronized with the upstream PoWFaucet repository
- **Protection**: Should be protected to only allow pulls from upstream
- **Updates**: Only updated when syncing with https://github.com/pk910/PoWFaucet
- **No Direct Changes**: Do not make Mordor-specific changes to this branch

## Mordor Branch (`mordor`)
- **Purpose**: Contains Mordor Testnet-specific configuration and customizations
- **Configuration**: 
  - Chain ID: 63
  - Network: Mordor Testnet
  - Ticker: mETC
  - Chain variable name: mordor
- **Updates**: 
  - Receives changes from `main` branch after upstream syncs
  - Contains Mordor-specific configuration files
  - May have Mordor-specific customizations

## Workflow

### Syncing with Upstream
1. Sync the `main` branch with upstream PoWFaucet repository
2. Merge changes from `main` into `mordor` branch
3. Resolve any conflicts, preferring Mordor-specific configurations
4. Test the faucet with Mordor configuration
5. Deploy to production

### Making Mordor-Specific Changes
1. Create a feature branch from `mordor`
2. Make your changes
3. Create a PR targeting the `mordor` branch
4. After review and CI passes, merge to `mordor`

## Configuration Files

### Mordor-Specific Configuration
- `faucet-config.mordor.yaml` - Mordor Testnet configuration file
  - RPC endpoint: https://rpc.mordor.etccooperative.org
  - Block explorer: https://explorer.mordor.etccooperative.org
  - Chain ID: 63
  - Token symbol: mETC
  - Uses legacy transactions (pre-EIP1559)

### Example Configuration
- `faucet-config.example.yaml` - Generic example configuration (from upstream)

## CI/CD Configuration

The following workflows are configured for the Mordor branch:

1. **test-build.yml**: Runs tests on PRs to `mordor` branch
2. **build-latest-v2.yml**: Builds binaries when pushing to `mordor` branch
3. Docker builds are triggered for both branches

## Branch Protection Recommendations

### For `main` branch:
- Require pull request reviews
- Require status checks to pass
- Only allow updates from upstream repository
- Do not allow direct pushes

### For `mordor` branch:
- Require pull request reviews for significant changes
- Require CI to pass before merging
- Allow direct pushes from maintainers for configuration updates
- Require at least one review for code changes

## Deployment

When deploying the Mordor faucet:
1. Use the `mordor` branch
2. Use the `faucet-config.mordor.yaml` configuration file
3. Ensure the RPC endpoint is accessible
4. Set appropriate environment variables for secrets (wallet key, captcha keys, etc.)

## Notes

- The `mordor` branch should always be deployable
- Keep Mordor-specific changes minimal and well-documented
- After major upstream updates, thoroughly test the Mordor configuration
- Document any deviations from upstream in this file or in commit messages
