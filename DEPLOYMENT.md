# Mordor Testnet Faucet Deployment Guide

This guide provides instructions for deploying the PoWFaucet for Mordor Testnet.

## Prerequisites

- Node.js 18 or higher
- Git
- A Mordor Testnet wallet with funds for the faucet
- (Optional) Docker for containerized deployment

## Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/mordortestnet/faucet.git
cd faucet
git checkout mordor
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Configure the Faucet

Copy the Mordor configuration file and customize it:

```bash
cp faucet-config.mordor.yaml faucet-config.yaml
```

Edit `faucet-config.yaml` and update the following critical settings:

- `faucetSecret`: Generate a random secret string (keep this secure!)
- `ethWalletKey`: Your faucet wallet private key (hex, without 0x prefix)
- `captcha.siteKey`: Your HCaptcha site key (or use a different captcha provider)
- `captcha.secret`: Your HCaptcha secret key

### 4. Build and Run

#### Option A: Run with Node.js (Recommended)

```bash
# Build the project
npm run build

# Build the client
npm run build-client

# Start the faucet
npm start
```

#### Option B: Use Bundled Version

```bash
# Create bundle
npm run bundle

# Run the bundle
node bundle/powfaucet.cjs
```

#### Option C: Docker Deployment

```bash
# Build the Docker image
docker build -t mordor-faucet .

# Run the container
docker run -d \
  -p 8080:8080 \
  -v $(pwd)/faucet-config.yaml:/app/faucet-config.yaml \
  -v $(pwd)/faucet-data:/app/data \
  --name mordor-faucet \
  mordor-faucet
```

### 5. Access the Faucet

The faucet will be available at `http://localhost:8080`

## Configuration Details

### Mordor Testnet Specifics

The Mordor configuration (`faucet-config.mordor.yaml`) is pre-configured with:

- **Chain ID**: 63
- **Network**: Mordor Testnet
- **RPC Endpoint**: https://rpc.mordor.etccooperative.org
- **Block Explorer**: https://explorer.mordor.etccooperative.org
- **Currency Symbol**: mETC
- **Transaction Type**: Legacy (pre-EIP1559)

### Important Configuration Options

#### Database
```yaml
database:
  driver: "sqlite"
  file: "faucet-store.db"
```

For production, consider using MySQL for better performance.

#### Drop Amounts
```yaml
maxDropAmount: 1000000000000000000 # 1 mETC
minDropAmount: 10000000000000000   # 0.01 mETC
```

Adjust based on your available funds and desired distribution rate.

#### Protection Modules

The faucet includes several protection mechanisms:
- **Captcha**: Prevents bot abuse
- **PoW Mining**: Requires computational work for rewards
- **IP-based limits**: Restricts hosting providers and proxies
- **Recurring limits**: Limits claims per address/IP over time
- **Balance checks**: Prevents claiming if already funded

See the [PoWFaucet Wiki](https://github.com/pk910/PoWFaucet/wiki) for detailed module configuration.

## Production Deployment

### Security Recommendations

1. **Protect Your Wallet Key**: Never commit your actual wallet private key to version control
2. **Use Environment Variables**: Store sensitive data in environment variables or a secrets manager
3. **HTTPS**: Use a reverse proxy (nginx, Apache) with SSL/TLS
4. **Firewall**: Restrict access to necessary ports only
5. **Regular Backups**: Backup your database regularly

### Using Environment Variables

You can override configuration values with environment variables:

```bash
export FAUCET_WALLET_KEY="your_private_key_here"
export FAUCET_SECRET="your_random_secret_here"
export CAPTCHA_SITE_KEY="your_captcha_site_key"
export CAPTCHA_SECRET="your_captcha_secret"
```

### Reverse Proxy Setup (nginx)

Example nginx configuration:

```nginx
server {
    listen 80;
    server_name faucet.mordor.example.com;
    
    location / {
        proxy_pass http://localhost:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

Update `httpProxyCount` in your config if using a reverse proxy:

```yaml
httpProxyCount: 1  # Set to number of proxies in front of faucet
```

### Systemd Service

Create `/etc/systemd/system/mordor-faucet.service`:

```ini
[Unit]
Description=Mordor Testnet PoW Faucet
After=network.target

[Service]
Type=simple
User=faucet
WorkingDirectory=/opt/mordor-faucet
ExecStart=/usr/bin/node bundle/powfaucet.cjs
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
sudo systemctl enable mordor-faucet
sudo systemctl start mordor-faucet
sudo systemctl status mordor-faucet
```

## Monitoring

### Logs

- **Faucet Events**: `faucet-events.log`
- **Faucet Stats**: `faucet-stats.log`

### Health Check

The faucet exposes a status endpoint:

```bash
curl http://localhost:8080/api/getStatus
```

## Troubleshooting

### Common Issues

1. **RPC Connection Errors**
   - Verify the RPC endpoint is accessible
   - Check firewall rules
   - Try an alternative RPC endpoint

2. **Low Funds Warning**
   - Transfer more mETC to the faucet wallet
   - Adjust `lowFundsBalance` threshold

3. **High Memory Usage**
   - Increase `sessionCleanup` to clean old sessions more frequently
   - Consider using MySQL instead of SQLite

4. **Captcha Not Working**
   - Verify captcha keys are correct
   - Check captcha provider status
   - Ensure domain is registered with captcha provider

## Updating

To update to the latest version:

```bash
git fetch origin
git checkout mordor
git pull origin mordor
npm install
npm run build
npm run build-client
# Restart the faucet
```

## Support

- **Upstream Project**: https://github.com/pk910/PoWFaucet
- **Documentation**: https://github.com/pk910/PoWFaucet/wiki
- **Mordor Testnet**: https://github.com/eth-classic/mordor

## Branch Strategy

This deployment uses the `mordor` branch, which is specifically configured for Mordor Testnet.
The `main` branch stays synchronized with the upstream PoWFaucet repository.

For more information, see [BRANCH_STRATEGY.md](BRANCH_STRATEGY.md).
