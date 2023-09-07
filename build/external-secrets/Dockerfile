FROM golang:1.19 as builder

ARG TARGETPLATFORM
ARG VERSION=v0.9.4

WORKDIR /external-secrets/external-secrets/build

RUN export GOOS=$(echo ${TARGETPLATFORM} | cut -d / -f1) && \
  export GOARCH=$(echo ${TARGETPLATFORM} | cut -d / -f2) && \
  GOARM=$(echo ${TARGETPLATFORM} | cut -d / -f3 | cut -c2-) && \
  git clone --depth 1 -b ${VERSION} \
    https://github.com/external-secrets/external-secrets.git . && \
  # go mod vendor && \
  go build

FROM alpine:3.17

RUN apk add --update --no-cache ca-certificates && \
  update-ca-certificates

COPY --from=builder \
    /external-secrets/external-secrets/build/external-secrets \
    /bin/external-secrets

# Run as UID for nobody since k8s pod securityContext runAsNonRoot can't resolve the user ID:
# https://github.com/kubernetes/kubernetes/issues/40958
USER 65534

ENTRYPOINT ["/bin/external-secrets"]
