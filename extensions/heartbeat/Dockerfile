ARG ELASTIC_VERSION

FROM docker.elastic.co/beats/heartbeat:${ELASTIC_VERSION}
COPY --chown=root:heartbeat heartbeat.yml /usr/share/heartbeat/heartbeat.yml
CMD ./heartbeat setup
CMD ./heartbeat -e
