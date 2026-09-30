# Genesis Outpost container image: the published release binary on a minimal base.
# Outbound HTTPS only, no inbound ports, no shell, non-root. Built by
# .github/workflows/image.yml from the files of a release in this repository.
FROM gcr.io/distroless/static-debian12:nonroot@sha256:afa5c872c891853ca7fcf1f12c3edb23f7eeef36189728842dd51042ff57f7ab
ARG TARGETARCH
COPY --chmod=0755 dist/genesis-outpost-linux-${TARGETARCH} /usr/local/bin/outpost
COPY dist/THIRD_PARTY_NOTICES /usr/share/doc/genesis-outpost/THIRD_PARTY_NOTICES
COPY LICENSE /usr/share/doc/genesis-outpost/LICENSE
# The last policy Genesis applied, restored at startup. Mount a volume here so it
# survives the container being replaced, not just restarted.
COPY --chown=10001:10001 --chmod=0700 image/state /var/lib/genesis-outpost
ENV OUTPOST_STATE_DIR=/var/lib/genesis-outpost
VOLUME ["/var/lib/genesis-outpost"]
USER 10001:10001
ENTRYPOINT ["/usr/local/bin/outpost", "run"]
