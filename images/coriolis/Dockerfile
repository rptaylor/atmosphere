# Copyright (c) 2024 VEXXHOST, Inc.
#
# Licensed under the Apache License, Version 2.0 (the "License"); you may
# not use this file except in compliance with the License. You may obtain
# a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
# WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
# License for the specific language governing permissions and limitations
# under the License.

ARG RELEASE

FROM registry.atmosphere.dev/library/openstack-venv-builder:${RELEASE} AS build
ARG CORIOLIS_GIT_REF=f15686c44ad71faa4e9ffd714988e754987f47de
ADD --keep-git-dir=true https://github.com/cloudbase/coriolis.git#${CORIOLIS_GIT_REF} /src/coriolis
RUN git -C /src/coriolis fetch --unshallow
COPY patches/coriolis /patches/coriolis
RUN git -C /src/coriolis apply --verbose /patches/coriolis/*
RUN --mount=type=cache,mode=0755,target=/root/.cache/pip,sharing=private <<EOF bash -xe
apt-get update -qq
apt-get install -qq -y --no-install-recommends pkg-config libmysqlclient-dev
pip3 install /src/coriolis
EOF

FROM registry.atmosphere.dev/library/openstack-python-runtime:${RELEASE}
COPY --from=build --link /var/lib/openstack /var/lib/openstack
