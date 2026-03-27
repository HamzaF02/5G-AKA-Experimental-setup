FROM ubuntu:22.04 AS builder

RUN apt-get update && apt-get install -y \
    git cmake make g++ libsctp-dev lksctp-tools \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /ueransim_src
COPY . .
RUN make -j$(nproc)

FROM ubuntu:22.04

RUN apt-get update && apt-get install -y \
    libsctp-dev iproute2 net-tools iputils-ping \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /ueransim/bin /ueransim/etc

# Binaries from fresh build
COPY --from=builder /ueransim_src/build/nr-ue   /ueransim/bin/nr-ue
COPY --from=builder /ueransim_src/build/nr-gnb  /ueransim/bin/nr-gnb
COPY --from=builder /ueransim_src/build/nr-cli  /ueransim/bin/nr-cli

# Config templates and entrypoint from old image
COPY docker/etc/custom-ue.yaml   /ueransim/etc/custom-ue.yaml
COPY docker/etc/custom-gnb.yaml  /ueransim/etc/custom-gnb.yaml
COPY docker/bin/entrypoint.sh    /ueransim/bin/entrypoint.sh
RUN chmod +x /ueransim/bin/entrypoint.sh

ENTRYPOINT ["/ueransim/bin/entrypoint.sh"]