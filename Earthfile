VERSION 0.8
FROM alpine

renovate-validate:
    # renovate: datasource=docker depName=renovate/renovate versioning=docker
    ARG RENOVATE_VERSION=37
    FROM renovate/renovate:$RENOVATE_VERSION
    WORKDIR /usr/src/app
    COPY renovate.json .
    RUN renovate-config-validator

shellcheck-lint:
    # renovate: datasource=docker depName=koalaman/shellcheck-alpine versioning=docker
    ARG SHELLCHECK_VERSION=v0.10.0
    FROM koalaman/shellcheck-alpine:$SHELLCHECK_VERSION
    WORKDIR /mnt
    COPY . .
    RUN find . -name "*.sh" -print | xargs -r -n1 shellcheck

lint:
    BUILD +shellcheck-lint

test:
    BUILD +renovate-validate
