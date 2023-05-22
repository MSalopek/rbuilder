FROM golang:1.20-bullseye

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get --no-install-recommends -y install \
    pciutils build-essential git wget \
    lsb-release dpkg-dev curl bsdmainutils fakeroot
RUN mkdir -p /usr/local/share/tendermint/

# Deploy the shell functions library.
COPY buildlib.sh /usr/local/share/tendermint/

# Create the 'builder' user.
RUN useradd -ms /bin/bash -U builder
ARG APP
ARG DEBUG
ARG TARGET_PLATFORMS
ENV APP ${APP:-app}
ENV DEBUG ${DEBUG}
ENV VERSION unknown
ENV COMMIT unknown
ENV LEDGER_ENABLE true
ENV TARGET_PLATFORMS ${TARGET_PLATFORMS:-linux/amd64}
ENV BUILD_SCRIPT ${BUILD_SCRIPT:-/sources/.build.sh}

# Drop root privileges.
USER builder:builder
WORKDIR /sources
# needed to avoid error: "fatal: detected dubious ownership in repository at '/sources'"
# check was added because of this vulnerability https://nvd.nist.gov/vuln/detail/cve-2022-24765
# more context: https://www.kenmuse.com/blog/avoiding-dubious-ownership-in-dev-containers/
RUN git config --global --add safe.directory /sources

# Mount the application's sources.
VOLUME [ "/sources" ]

# Run the application's build.sh.
ENTRYPOINT [ "/bin/bash", "-c", "${BUILD_SCRIPT}" ]
