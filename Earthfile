VERSION 0.8
FROM alpine

###############################################################################
# Primary Targets
###############################################################################

build-image:
    ARG TAG=dev
    FROM DOCKERFILE \
        -f docker/Dockerfile \
        docker
    SAVE IMAGE --push factorio-server-manager:$TAG

lint:
    BUILD +hadolint
    BUILD +shellcheck-lint

test:
    BUILD +go-test
    BUILD +renovate-validate

###############################################################################
# Individual Targets
###############################################################################

go-test:
    # renovate: datasource=docker depName=golang
    ARG GOLANG_VERSION=1.18
    FROM golang:$GOLANG_VERSION
    WORKDIR /usr/src/app
    COPY conf.json.example conf.json
    COPY --dir src .
    WORKDIR src
    # Setup Tests to look for things in the right place...
    ENV FSM_CONF ../../conf.json
    ENV FSM_DIR ../
    ENV FSM_MODPAKC_DIR dev_pack
    RUN go test ./... -v -test.short

hadolint:
    # renovate: datasource=docker depName=hadolint/hadolint versioning=docker
    ARG HADOLINT_VERSION=2.12.0-alpine
    FROM hadolint/hadolint:$HADOLINT_VERSION
    WORKDIR /images
    COPY .hadolint.yaml .
    COPY docker/Dockerfile* .
    RUN find . -name "Dockerfile*" -print | xargs -r -n1 hadolint

renovate-validate:
    # renovate: datasource=docker depName=renovate/renovate versioning=docker
    ARG RENOVATE_VERSION=38
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
