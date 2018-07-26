FROM debian:stable-slim

RUN export DEBIAN_FRONTEND=noninteractive && \
    apt-get update && \
    apt-get install -y --no-install-recommends lsb-release && \
    echo "deb http://deb.debian.org/debian $(lsb_release -cs)-backports main" \
        >>/etc/apt/sources.list.d/backports.list && \
    apt-get update && \
    mkdir /usr/share/man/man1 && \
    apt-get install -y --no-install-recommends openjdk-9-jdk-headless && \
    rm -v /etc/apt/sources.list.d/backports.list && \
    apt-get update && \
    apt-get install -y --no-install-recommends \
        git \
        gpg \
        openssh-client && \
    apt-get purge -y lsb-release && \
    apt-get autoremove --purge -y && \
    rm -rv /var/lib/apt/lists/*

# Add user with same uid and gid as build agent for easier file sharing
RUN groupadd -g 1001 teamcity && \
    useradd --no-log-init --shell /bin/false --create-home -g teamcity -u 1001 teamcity

# Add Gradle init script to a volume that overlays the home-dir mount to be added later
COPY --chown=teamcity:teamcity build-cache.gradle /home/teamcity/.gradle/init.d/
VOLUME /home/teamcity/.gradle/init.d/

USER teamcity

# Add github.com to known hosts file
RUN ssh -o StrictHostKeyChecking=no -o RequestTTY=no git@github.com || true
