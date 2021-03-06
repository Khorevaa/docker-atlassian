FROM tommylau/java:1.8

MAINTAINER Tommy Lau <tommy@gen-new.com>

# Setup useful environment variables
ENV CONFLUENCE_HOME     /var/atlassian/application-data/confluence
ENV CONFLUENCE_INSTALL  /opt/atlassian/confluence
ENV CONFLUENCE_VERSION  5.10.8
ENV DOWNLOAD_URL        https://downloads.atlassian.com/software/confluence/downloads/atlassian-confluence-${CONFLUENCE_VERSION}.tar.gz

LABEL Description="This image is used to start Atlassian Confluence" Vendor="Tommy Lau" Version="${CONFLUENCE_VERSION}"

# Use the default unprivileged account. This could be considered bad practice
# on systems where multiple processes end up being executed by 'daemon' but
# here we only ever run one process anyway.
ENV RUN_USER            daemon
ENV RUN_GROUP           daemon

# Install Atlassian Confluence and helper tools and setup initial home
# directory structure.
RUN set -x \
    && mkdir -p                         "${CONFLUENCE_HOME}" \
    && chmod -R 700                     "${CONFLUENCE_HOME}" \
    && chown ${RUN_USER}:${RUN_GROUP}   "${CONFLUENCE_HOME}" \
    && mkdir -p                         "${CONFLUENCE_INSTALL}/conf" \
    && curl -Ls                         "${DOWNLOAD_URL}" | tar -xz --strip=1 -C "$CONFLUENCE_INSTALL" \
    && chmod -R 700                     "${CONFLUENCE_INSTALL}/conf" \
    && chmod -R 700                     "${CONFLUENCE_INSTALL}/logs" \
    && chmod -R 700                     "${CONFLUENCE_INSTALL}/temp" \
    && chmod -R 700                     "${CONFLUENCE_INSTALL}/work" \
    && cd ${CONFLUENCE_INSTALL}/confluence/WEB-INF/lib \
    && curl -SLO "https://gist.github.com/TommyLau/8a5ce4629d027f7884e6/raw/18fb5f18b5cf7997a37169c0ba4f17e2e38c7c96/atlassian-extras-decoder-v2-3.2.jar" \
    && cd ../atlassian-bundled-plugins/ \
    && curl -SLO "https://gist.github.com/TommyLau/8a5ce4629d027f7884e6/raw/0cb48f928f125cb494a57d05ed9de0cba913d42a/atlassian-universal-plugin-manager-plugin-2.21.3.jar" \
    && chown -R ${RUN_USER}:${RUN_GROUP} ${CONFLUENCE_INSTALL} \
    && echo "confluence.home=${CONFLUENCE_HOME}" > ${CONFLUENCE_INSTALL}/confluence/WEB-INF/classes/confluence-init.properties \
    && mv ${CONFLUENCE_INSTALL}/confluence/WEB-INF/lib/mail-*.jar ${CONFLUENCE_INSTALL}/lib

# Use the default unprivileged account. This could be considered bad practice
# on systems where multiple processes end up being executed by 'daemon' but
# here we only ever run one process anyway.
USER ${RUN_USER}:${RUN_GROUP}

# Expose default HTTP connector port.
EXPOSE 8090

# Set volume mount points for installation and home directory. Changes to the
# home directory needs to be persisted as well as parts of the installation
# directory due to eg. logs.
VOLUME ["${CONFLUENCE_INSTALL}", "${CONFLUENCE_HOME}"]

# Set the default working directory as the Confluence installation directory.
WORKDIR ${CONFLUENCE_INSTALL}

# Run in foreground
CMD ["./bin/start-confluence.sh", "-fg"]
