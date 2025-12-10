# BJJ FX Manager Documentation

Welcome to the BJJ FX Manager documentation! This directory contains comprehensive guides for all aspects of using BJJ FXM.

## Getting Started

- **[Main README](../readme.md)** - Overview, installation, and quick start
- **[Production Deployment Guide](production-guide.md)** - Complete setup guide for production servers

## Core Guides

### [TypeScript Resource Management](typescript-resources.md)
Learn how BJJ FXM automatically builds and manages TypeScript resources:
- Automatic detection and compilation
- Build process explained
- Isolated build environments
- Resource structure best practices
- Configuration examples

### [Production Guide](production-guide.md)
Everything you need to deploy and manage FiveM servers in production:
- Linux server setup and optimization
- Security configuration
- Monitoring and alerts
- Backup strategies
- Multi-server management
- Automated maintenance tasks

### [Performance Analysis](performance-analysis.md)
Detailed performance metrics and cost savings analysis:
- Linux vs Windows benchmarks
- BJJ FXM performance improvements
- Real-world production testing results
- Cost savings breakdown
- Scalability analysis

### [Troubleshooting](troubleshooting.md)
Solutions to common issues:
- Server startup problems
- Build failures
- File watching issues
- Performance problems
- Configuration issues
- Diagnostic tools and techniques

### [Advanced Usage](advanced-usage.md)
Advanced features and integration patterns:
- Custom build scripts
- Environment variables
- Resource-specific configuration
- CI/CD integration (GitHub Actions, GitLab CI, Jenkins)
- Multi-environment setup
- Custom deployment scripts
- Programmatic API usage

## Quick Links

### Common Tasks
- [Install BJJ FXM](../readme.md#installation)
- [Initialize a server](../readme.md#quick-start)
- [Configure auto-restart](production-guide.md#step-5-configure-for-production)
- [Set up webhooks](production-guide.md#webhook-configuration)
- [Clear cache](../readme.md#fxm-clear-cache)

### Troubleshooting Quick Fixes
- [Server won't start](troubleshooting.md#server-wont-start)
- [Build failures](troubleshooting.md#typescript-build-failures)
- [File watching not working](troubleshooting.md#watch-mode-not-detecting-changes)
- [High CPU/memory usage](troubleshooting.md#performance-issues)

### Advanced Topics
- [Custom build scripts](advanced-usage.md#custom-build-scripts)
- [CI/CD integration](advanced-usage.md#cicd-integration)
- [Blue-green deployment](advanced-usage.md#blue-green-deployment)
- [Systemd service setup](production-guide.md#security-configuration)

## Documentation Structure

```
docs/
├── README.md (this file)          # Documentation index
├── typescript-resources.md        # TypeScript build system guide
├── production-guide.md            # Production deployment and maintenance
├── performance-analysis.md        # Performance metrics and benchmarks
├── troubleshooting.md            # Common issues and solutions
└── advanced-usage.md             # Advanced features and integrations
```

## Contributing to Documentation

If you find errors or want to improve the documentation:

1. Fork the repository
2. Make your changes
3. Submit a pull request

All documentation is written in Markdown and located in the `docs/` directory.

## License

This documentation is licensed under the [Creative Commons Attribution 4.0 International License (CC BY 4.0)](LICENSE.md).

You are free to use, share, and adapt this documentation with proper attribution.

---

[Back to Main README](../readme.md)
