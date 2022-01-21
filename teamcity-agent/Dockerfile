FROM jetbrains/teamcity-minimal-agent:latest AS teamcity-minimal-agent

FROM debian:stable-slim

RUN export DEBIAN_FRONTEND=noninteractive && \
    apt-get update && \
    # add packages needed to build the image
    apt-get install -y --no-install-recommends \
        apt-transport-https \
        ca-certificates \
        dirmngr \
        gnupg \
        lsb-release \
        patch && \
    # add docker key
    APT_KEY_DONT_WARN_ON_DANGEROUS_USAGE=true \
        apt-key adv --fetch-keys https://download.docker.com/linux/debian/gpg && \
    # add docker repo
    echo "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable" \
        >>/etc/apt/sources.list.d/docker.list && \
    apt-get update && \
    # install docker, git, java, procps and openssh-client
    mkdir /usr/share/man/man1 && \
    apt-get install -y --no-install-recommends \
        docker-ce \
        git \
        openjdk-8-jre-headless \
        procps \
        openssh-client && \
    # add user with same uid and gid as build image for easier file sharing
    groupadd -g 1001 teamcity && \
    useradd --no-log-init --shell /bin/false --create-home -g teamcity -u 1001 teamcity && \
    # give the user docker control rights
    adduser teamcity docker && \
    # clean up after apt-get
    apt-get purge -y \
        dirmngr \
        gnupg \
        lsb-release \
        patch && \
    apt-get autoremove --purge -y && \
    rm -rv /var/lib/apt/lists/*

COPY --from=teamcity-minimal-agent /run-agent.sh /
CMD ["/run-agent.sh"]

# replace the build agent directory by a unique one to be able to properly use the docker wrapper with the outer daemon
ARG i
ENV INDEX=${i}
LABEL teamcity.agent.index=${i}
COPY --from=teamcity-minimal-agent --chown=teamcity:teamcity /opt/buildagent/ /opt/buildagent${i}/
RUN install -d -m 0755 -o teamcity -g teamcity /opt/buildagent${i}/conf/ && \
    sed -i "s!/opt/buildagent!/opt/buildagent${i}!" /run-agent.sh && \
    sed -i "s!/data/teamcity_agent!/opt/buildagent${i}!" /run-agent.sh
VOLUME /opt/buildagent${i}

USER teamcity

# Add javacord-bot server to known hosts file
RUN ssh -o BatchMode=yes -o StrictHostKeyChecking=no -o RequestTTY=no teamcity@159.69.82.177 || true
