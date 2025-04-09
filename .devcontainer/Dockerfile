FROM "mcr.microsoft.com/devcontainers/javascript-node:1-18-bullseye"

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    # Remove imagemagick due to https://security-tracker.debian.org/tracker/CVE-2019-10131
    && apt-get purge -y imagemagick imagemagick-6-common \
    # This container will also function as somewhat of a test platform for new software, hence the extra packages.
    && apt-get install -y --no-install-recommends vim screen ripgrep wget curl gdebi ca-certificates gnupg2 htop \
    # Github CLI: https://github.com/cli/cli/blob/trunk/docs/install_linux.md
	&& mkdir -p -m 755 /etc/apt/keyrings \
        && out=$(mktemp) && wget -nv -O$out https://cli.github.com/packages/githubcli-archive-keyring.gpg \
        && cat $out | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
	&& chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
	&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
	&& apt-get update \
	&& apt-get install gh -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/{lists,log,cache}

RUN mkdir -p /var/runtime && \
    wget --https-only --secure-protocol=TLSv1_3 -O vscodecli.tar.gz 'https://code.visualstudio.com/sha/download?build=stable&os=cli-alpine-x64' && \
    tar -zxvf vscodecli.tar.gz -C /var/runtime && \
    rm -f vscodecli.tar.gz

# Tailscale usermode networking: https://tailscale.com/kb/1113/aws-lambda
COPY --from=docker.io/tailscale/tailscale:stable /usr/local/bin/tailscaled /var/runtime/tailscaled
COPY --from=docker.io/tailscale/tailscale:stable /usr/local/bin/tailscale /var/runtime/tailscale
RUN mkdir -p /tmp/tailscale && \
    mkdir -p /var/run && ln -s /tmp/tailscale /var/run/tailscale && \
    mkdir -p /var/cache && ln -s /tmp/tailscale /var/cache/tailscale && \
    mkdir -p /var/lib && ln -s /tmp/tailscale /var/lib/tailscale && \
    mkdir -p /var/task && ln -s /tmp/tailscale /var/task/tailscale
