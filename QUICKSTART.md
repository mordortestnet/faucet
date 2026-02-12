# Mordor Faucet Quick Reference

## Quick Deploy with Docker

```bash
# Clone and checkout mordor branch
git clone https://github.com/mordortestnet/faucet.git
cd faucet
git checkout mordor

# Create your config from the Mordor template
cp faucet-config.mordor.yaml faucet-config.yaml

# Edit config (set your secrets)
nano faucet-config.yaml
# Required: faucetSecret, ethWalletKey, captcha keys

# Build and run
docker build -t mordor-faucet .
docker run -d -p 8080:8080 \
  -v $(pwd)/faucet-config.yaml:/app/faucet-config.yaml \
  --name mordor-faucet \
  mordor-faucet
```

## Quick Deploy with Node.js

```bash
# Clone and checkout mordor branch
git clone https://github.com/mordortestnet/faucet.git
cd faucet
git checkout mordor

# Install and build
npm install
npm run build
npm run build-client

# Create your config
cp faucet-config.mordor.yaml faucet-config.yaml
nano faucet-config.yaml

# Run
npm start
```

## Mordor Network Details

| Property | Value |
|----------|-------|
| Chain ID | 63 |
| Network | Mordor Testnet |
| Currency | mETC |
| RPC | https://rpc.mordor.etccooperative.org |
| Explorer | https://explorer.mordor.etccooperative.org |

## Required Configuration

Edit `faucet-config.yaml` and set:

```yaml
faucetSecret: "YOUR_RANDOM_SECRET_HERE"
ethWalletKey: "YOUR_WALLET_PRIVATE_KEY_WITHOUT_0x"
modules:
  captcha:
    siteKey: "YOUR_CAPTCHA_SITE_KEY"
    secret: "YOUR_CAPTCHA_SECRET"
```

## Common Tasks

### Check faucet status
```bash
curl http://localhost:8080/api/getStatus
```

### View logs
```bash
tail -f faucet-events.log
tail -f faucet-stats.log
```

### Restart faucet
```bash
# Docker
docker restart mordor-faucet

# Node.js
npm start
```

## Important URLs

- **Deployment Guide**: [DEPLOYMENT.md](DEPLOYMENT.md)
- **Branch Strategy**: [BRANCH_STRATEGY.md](BRANCH_STRATEGY.md)
- **Configuration Summary**: [CONFIGURATION_SUMMARY.md](CONFIGURATION_SUMMARY.md)
- **Upstream Wiki**: https://github.com/pk910/PoWFaucet/wiki

## Default Settings

- Max drop: 1 mETC
- Min drop: 0.01 mETC
- Server port: 8080
- Session timeout: 12 hours
- Uses legacy transactions (pre-EIP1559)

## Getting Help

1. Check [DEPLOYMENT.md](DEPLOYMENT.md) for detailed instructions
2. Review logs in `faucet-events.log`
3. Check RPC connectivity: `curl https://rpc.mordor.etccooperative.org`
4. Verify wallet has funds on Mordor testnet
5. Ensure captcha keys are valid

## Security Checklist

- [ ] Change `faucetSecret` to a random string
- [ ] Set your own `ethWalletKey` (never commit to git)
- [ ] Configure valid captcha keys
- [ ] Use HTTPS in production
- [ ] Set `httpProxyCount` if behind reverse proxy
- [ ] Enable firewall rules
- [ ] Regular backups of database
- [ ] Monitor faucet balance

## Branches

- **main**: Synced with upstream, no Mordor changes
- **mordor**: Configured for Mordor, use for deployment
