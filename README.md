# 🚀 Distroless MTProto Proxy: Hardened image

[![CI/CD](https://github.com/ammnt/MTProxy/actions/workflows/build.yml/badge.svg)](https://github.com/ammnt/MTProxy/actions/workflows/build.yml)
[![GitHub stars](https://img.shields.io/github/stars/ammnt/MTProxy.svg)](https://github.com/ammnt/MTProxy/stargazers)
![Feature](https://img.shields.io/badge/feature-distroless-blue)
[![GitHub issues open](https://img.shields.io/github/issues/ammnt/MTProxy.svg)](https://github.com/ammnt/MTProxy/issues)
![GitHub Maintained](https://img.shields.io/badge/open%20source-yes-orange)
![GitHub Maintained](https://img.shields.io/badge/maintained-yes-yellow)

> **Production-ready, security-focused MTProto Proxy for Telegram with minimal attack surface.**

> [!IMPORTANT]
> This is the official Telegram MTProto proxy, not third-party implementations. Fully compatible with all Telegram clients⚠️

> [!TIP]
> Use TLS mode (`-D` flag) to make traffic indistinguishable from HTTPS - recommended for censored networks💡

> [!IMPORTANT]
> UID/GID is set to `10480` - prevents conflicts with system users and follows security best practices⚠️

## 📦 Quick Start

### Generate Secret Key and TLS secret (ee + random + domain in hex)
```bash
# Generate random 16-byte hex key (32 hex characters)
DOMAIN="example.com"
DOMAIN_HEX=$(echo -n $DOMAIN | xxd -ps)
RANDOM_HEX=$(head -c 16 /dev/urandom | xxd -ps)
TLS_SECRET="ee${RANDOM_HEX}${DOMAIN_HEX}"
echo "Your secret key: $TLS_SECRET"

docker run -d \
  --name mtproxy \
  -p 443:3478 \
  -p 8888:8888 \
  --restart unless-stopped \
  ammnt/mtproxy:slim \
  -S $RANDOM_HEX \
  -D $DOMAIN
```

### Docker Compose (Recommended)
```yaml
services:
  mtproto:
    image: ammnt/mtproxy:slim
    container_name: mtproxy
    restart: unless-stopped
    ports:
      - "443:3478"
      - "8888:8888"
    user: "10480:10480"
    read_only: true
    privileged: false
    tmpfs:
      - /tmp:mode=1700,size=100M,noexec,nosuid,nodev,uid=10480,gid=10480
    cap_drop:
      - ALL
    security_opt:
      - no-new-privileges=true
    command:
      - "-S"
      - "${RANDOM_HEX}"
      - "-D"
      - "${DOMAIN}"
```

## 🔥 Why Choose This Image?

### **Hardened Security**
- **Distroless base** - built from `scratch` with zero bloat, no shell, no package manager
- **Minimal attack surface** - only the binary and shared libraries in the final image
- **Rootless by design** - runs as non-root user `mtproxy` (UID/GID 10480)
- **CIS Docker Benchmark** - follows industry security best practices
- **Stripped symbols** - no debugging information in production
- **UPX compressed** - minimal memory footprint with fast decompression
- **Pinned dependencies** - exact versions for all build packages
- **Minimal layers** - optimized Docker layer caching
- **Efficient logging** - direct to stdout for container integration
- **Graceful shutdown** - SIGQUIT handling for connection draining
- **Comprehensive labels** - full OCI metadata compliance

## 🤝 Contributing & Support

Found an issue or have an improvement?
- [Open an Issue](https://github.com/ammnt/MTProxy/issues/new)
- [Feature Request](https://github.com/ammnt/MTProxy/issues/new?template=feature_request.md)

## 📄 License

This project is open source and maintained with ❤️ by [ammnt](https://msftcnsi.com)

Based on the official [Telegram MTProxy](https://github.com/TelegramMessenger/MTProxy) under GPLv2 license.