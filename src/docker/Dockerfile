# This Dockerfile is intentionally vulnerable for testing purposes.
# It has been created with AI assistance.

# Outdated/EOL base: many known CVEs
FROM debian:9.2

LABEL maintainer="you@example.com"

# Hardcoded secrets in environment variables (secret scanners will flag them)
ENV AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
ENV AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
ENV DB_PASSWORD="password123!"

# Remote 'ADD' without checksum verification (supply-chain risk)
#    (Downloads a public file just to trigger scanners)
ADD https://raw.githubusercontent.com/github/gitignore/main/Java.gitignore /tmp/remote.txt

# Packages without version pinning, no signature verification, and no cache cleanup
# Installs unnecessary services (openssh-server) and networking tools
RUN apt-get update && \
    apt-get upgrade -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y \
      curl wget ca-certificates \
      openssh-server sudo \
      netcat telnet \
      python \
    && mkdir -p /var/run/sshd
# (deliberately NOT running 'rm -rf /var/lib/apt/lists/*')

WORKDIR /app

# Copies entire context (risk of including .env, keys, build cache, etc.)
COPY . /app

# Excessive permissions
RUN chmod -R 777 /app

# Enables root login via SSH with weak password
RUN echo 'root:root' | chpasswd && \
    sed -i 's/#\?PermitRootLogin .*/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -i 's/#\?PasswordAuthentication .*/PasswordAuthentication yes/' /etc/ssh/sshd_config

# Creates a fake private key inside the image (secret scanners will detect it)
RUN mkdir -p /root/.ssh && \
    printf "-----BEGIN PRIVATE KEY-----\nMIIB...FAKE...KEY\n-----END PRIVATE KEY-----\n" > /root/.ssh/id_rsa && \
    chmod 600 /root/.ssh/id_rsa

# Exposes unnecessary ports
EXPOSE 22 8080

# Runs as root (no non-root USER specified)
CMD ["/usr/sbin/sshd","-D"]
