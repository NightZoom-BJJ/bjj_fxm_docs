# BJJ FX Manager (FXM)

BJJ FX Manager is a professional CLI tool designed to drastically improve the usability and performance of FiveM/FX servers in remote and production environments. It addresses critical challenges in running FX servers on Linux systems by providing comprehensive automation, resource management, and process isolation that traditional manual workflows cannot achieve.

## The Problem This Solves

Running FiveM servers on Linux is both cost-effective and performance-optimal compared to Windows environments. However, FX server instances present unique operational challenges:

- **Cannot be containerized** (Docker/etc.) due to FiveM's architecture requirements
- **Must run in persistent terminal sessions**, making remote management difficult
- **Require constant manual intervention** for builds, restarts, and cache management
- **Desktop Linux environments** eliminate most performance gains from using Linux
- **TypeScript resource management** has no native automation in FiveM's ecosystem

Terminal-based Ubuntu servers are the most efficient way to run production FiveM servers (as used by [NightZoom©](https://nightzoom.net)), but manually managing server processes, TypeScript builds, and resource deployments via SSH is error-prone and time-consuming.

BJJ FX Manager solves this by automating every aspect of FX server lifecycle management while running all build processes and resource management in isolated Node.js contexts, keeping your Linux environment clean and your server performance optimal.

## Key Features

**Enterprise Server Management**
- Complete server lifecycle control with intelligent process management
- Automated crash detection and configurable restart policies
- Real-time status monitoring and health checks
- Cross-platform support (Windows, Linux, macOS)

**Advanced TypeScript Build System**
- Automatic detection and compilation of TypeScript resources
- Intelligent dependency resolution and installation
- Custom build script integration with fallback compilation
- Isolated build environments to prevent system contamination

**Intelligent Resource Handling**
- Automated resource detection and validation
- Template-based resource generation for rapid development
- Debug configuration detection and production safety checks
- Hot-reload support with file watching and auto-rebuild

**Production-Ready Cache Management**
- Automatic cache cleanup to prevent disk bloat
- Configurable cache policies with dry-run capabilities
- Safe deletion with confirmation prompts and backups

**Deployment Automation**
- Git integration for remote code deployment
- Script-based and direct executable deployment modes
- Auto-detection of deployment configurations
- One-command deployment from repository to running server

**Developer Experience**
- YAML-based configuration with sensible defaults
- Comprehensive logging system with rotation
- Webhook notifications (Discord, Slack) for monitoring
- Template library with JavaScript, Lua, and TypeScript examples

## Why It Matters

BJJ FXM delivers **significant performance and cost improvements** for FiveM servers:

- **50-70% faster** TypeScript builds with isolated contexts
- **85-90% faster** deployment workflows
- **95%+ reduction** in crash-related downtime
- **50-65% reduction** in infrastructure costs
- **90% time savings** on manual management tasks

**[Read the full performance analysis →](docs/performance-analysis.md)**

## Installation

Install globally via npm:

```bash
npm install -g bjj_fxm
```

Or install from source:

```bash
git clone https://github.com/NightZoom-BJJ/bjj_fxm.git
cd bjj_fxm
npm install
npm run build
npm link
```

## Quick Start

### First-Time Setup

1. **Initialize** your FiveM server directory:
   ```bash
   cd /path/to/your-fivem-server
   fxm init
   ```

2. **Build** all TypeScript resources:
   ```bash
   fxm build
   ```

3. **Start** your server with automation:
   ```bash
   fxm start --watch --restart
   ```

### Production Deployment

For production Linux servers:

```bash
# Clone your server repository
git clone https://github.com/your-org/your-server.git
cd your-server

# Initialize BJJ FXM
fxm init

# Configure for production
fxm config --set autoRestart=true
fxm config --set buildBeforeStart=true

# Start with auto-restart
fxm start --restart
```

**[View complete production setup guide →](docs/production-guide.md)**

## Command Reference

### Core Commands

| Command | Description |
|---------|-------------|
| `fxm init` | Initialize FXM in server directory |
| `fxm start [--watch] [--restart]` | Start server with optional features |
| `fxm stop` | Stop running server |
| `fxm build [--watch]` | Build TypeScript resources |
| `fxm status` | Display server status and metrics |
| `fxm clear-cache [--dry-run] [--force]` | Clear server cache files |
| `fxm generate` | Generate new resource from template |
| `fxm debug-check [--webhook]` | Check for debug configurations |
| `fxm config [--show] [--set key=value]` | Manage configuration |

### Example Usage

```bash
# Development: watch mode for hot-reload
fxm start --watch --restart

# Production: auto-restart only
fxm start --restart

# Build with watch mode
fxm build --watch

# Check status
fxm status

# Clear cache preview
fxm clear-cache --dry-run
```

## Configuration

BJJ FXM uses `fxm.config.yml` for configuration:

```yaml
# Server settings
serverProfile: production
serverExecutable: ./FXServer
resourcesPath: resources
serverConfigFile: server.cfg

# Automation
autoRestart: true
maxRestarts: 10
restartDelay: 5000
buildBeforeStart: true
watchResources: false

# Logging
logLevel: info

# Notifications
notifications:
  discord:
    enabled: true
    webhook: "YOUR_WEBHOOK_URL"

# Resource build settings
resources:
  exclude:
    - node_modules
    - .git
    - dist
  buildTimeout: 120000
```

## Documentation

### Getting Started
- [Installation & Quick Start](#installation) (above)
- [Production Deployment Guide](docs/production-guide.md)
- [TypeScript Resource Management](docs/typescript-resources.md)

### Performance & Optimization
- [Performance Analysis & Cost Savings](docs/performance-analysis.md)
- [Linux Server Optimization](docs/production-guide.md#linux-server-setup)

### Guides & References
- [Troubleshooting Guide](docs/troubleshooting.md)
- [Advanced Usage](docs/advanced-usage.md)
- [CI/CD Integration](docs/advanced-usage.md#cicd-integration)

## TypeScript Resources

BJJ FXM automatically detects and builds TypeScript resources with zero configuration. It intelligently:

- Scans for `.ts` files and `tsconfig.json`
- Installs dependencies as needed
- Runs custom build scripts or falls back to `tsc`
- Builds in isolated contexts to keep your system clean

**Recommended resource structure:**

```
resources/
├── my-resource/
│   ├── src/
│   │   ├── client/
│   │   ├── server/
│   │   └── shared/
│   ├── dist/              # Build output
│   ├── fxmanifest.lua
│   ├── package.json
│   └── tsconfig.json
```

**[Learn more about TypeScript resources →](docs/typescript-resources.md)**

## Production Best Practices

### Recommended Setup

- **OS**: Ubuntu Server 22.04 LTS (terminal only, no desktop)
- **Process**: Run as dedicated non-root user
- **Security**: Configure firewall for FiveM ports
- **Monitoring**: Enable webhook notifications
- **Backups**: Automated daily backups with retention
- **Maintenance**: Scheduled cache cleanup and health checks

### Quick Production Checklist

- [ ] Initialize FXM: `fxm init`
- [ ] Enable auto-restart: `fxm config --set autoRestart=true`
- [ ] Configure webhooks for alerts
- [ ] Set up firewall rules (port 30120)
- [ ] Create systemd service for auto-start
- [ ] Schedule cache cleanup (weekly cron)
- [ ] Configure automated backups

**[View complete production guide →](docs/production-guide.md)**

## Troubleshooting

### Common BJJ FXM Issues

**FXM won't start server:**
```bash
fxm config --show        # Verify FXM configuration
cat logs/error.log       # Check FXM error logs
ls -la ./FXServer        # Verify executable exists
```

**Build failures:**
```bash
cat logs/build.log       # Check build errors
cd resources/my-resource
npm install              # Reinstall dependencies
npm run build            # Test manual build
```

**File watching not working (Linux):**
```bash
# Increase inotify limit
echo "fs.inotify.max_user_watches=524288" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

**Monitor your server with glances:**
```bash
# Install glances - excellent all-in-one monitoring tool
sudo apt-get install glances
glances
```

**Note:** BJJ FXM manages server processes and builds TypeScript resources. For FiveM server issues (server.cfg, resources, networking), consult FiveM documentation.

**[View complete troubleshooting guide →](docs/troubleshooting.md)**

## Contributing

Contributions are welcome! To contribute:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Commit changes: `git commit -m 'Add amazing feature'`
4. Push to branch: `git push origin feature/amazing-feature`
5. Open a Pull Request

### Development Setup

```bash
git clone https://github.com/NightZoom-BJJ/bjj_fxm.git
cd bjj_fxm
npm install
npm run build
npm link
```

## License

**Software:** Proprietary and confidential. See the [LICENSE](LICENSE) file for details.

**Documentation:** The documentation in the `docs/` directory is licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](docs/LICENSE.md). You are free to use, share, and adapt the documentation with attribution.

## Credits

**BJJ FX Manager** is developed and maintained by bjj_dev

**Special Thanks:**
- [NightZoom©](https://nightzoom.net) Dev's & Community
- The FiveM community

**Used in Production By:**
- [NightZoom©](https://nightzoom.net)

## Support

- **Documentation**: [docs/](docs/)
- **Issues**: [GitHub Issues](https://github.com/NightZoom-BJJ/bjj_fxm/issues)
- **Repository**: [github.com/NightZoom-BJJ/bjj_fxm](https://github.com/NightZoom-BJJ/bjj_fxm)

---

**Check out Nightzoom :) :** [NightZoom©](https://nightzoom.net)

**Version:** 1.0.5 | **License:** MIT
