FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# Install dependencies + Neofetch
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       locales openssh-server wget ca-certificates curl gnupg neofetch \
    && localedef -i en_US -c -f UTF-8 en_US.UTF-8 \
    && rm -rf /var/lib/apt/lists/*

# Install Node.js 24
RUN curl -fsSL https://deb.nodesource.com/setup_24.x | bash - \
    && apt-get install -y nodejs

# Install cloudflared
RUN wget -q \
    https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64 \
    -O /usr/local/bin/cloudflared \
    && chmod +x /usr/local/bin/cloudflared

# Setup SSH
RUN mkdir -p /run/sshd \
    && echo 'PermitRootLogin yes' >> /etc/ssh/sshd_config \
    && echo 'PasswordAuthentication yes' >> /etc/ssh/sshd_config

# Setup start script
RUN printf '%s\n' \
    '#!/bin/bash' \
    'set -e' \
    ': "${ROOT_PASSWORD:?Set ROOT_PASSWORD in Railway Variables}"' \
    ': "${CLOUDFLARE_TUNNEL_TOKEN:?Set CLOUDFLARE_TUNNEL_TOKEN in Railway Variables}"' \
    'echo "root:${ROOT_PASSWORD}" | chpasswd' \
    '/usr/sbin/sshd' \
    'exec cloudflared tunnel --no-autoupdate run --token "$CLOUDFLARE_TUNNEL_TOKEN"' \
    > /usr/local/bin/start-vps \
    && chmod +x /usr/local/bin/start-vps

EXPOSE 22

CMD ["/usr/local/bin/start-vps"]
