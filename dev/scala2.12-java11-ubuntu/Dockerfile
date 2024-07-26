#
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
FROM eclipse-temurin:11-jre-focal AS staging
ARG SPARK_TGZ_FILE

WORKDIR /tmp/spark

COPY ${SPARK_TGZ_FILE} ./

RUN tar -xvf ${SPARK_TGZ_FILE} --strip-components=1

FROM eclipse-temurin:11-jre-focal

ARG spark_uid=185

RUN groupadd --system --gid=${spark_uid} spark && \
    useradd --system --create-home --uid=${spark_uid} --gid=spark spark

RUN set -ex; \
    apt-get update; \
    apt-get install -y gnupg2 wget bash tini libc6 libpam-modules krb5-user libnss3 procps net-tools gosu libnss-wrapper; \
    mkdir -p /opt/spark; \
    mkdir /opt/spark/python; \
    mkdir -p /opt/spark/examples; \
    mkdir -p /opt/spark/work-dir; \
    chmod g+w /opt/spark/work-dir; \
    touch /opt/spark/RELEASE; \
    chown -R spark:spark /opt/spark; \
    echo "auth required pam_wheel.so use_uid" >> /etc/pam.d/su; \
    rm -rf /var/lib/apt/lists/*

COPY --chown=spark:spark --from=staging /tmp/spark/jars /opt/spark/jars/
COPY --chown=spark:spark --from=staging /tmp/spark/bin /opt/spark/bin/
COPY --chown=spark:spark --from=staging /tmp/spark/sbin /opt/spark/sbin/
#COPY --chown=spark:spark --from=staging --chmod=a+x /tmp/spark/kubernetes/dockerfiles/spark/decom.sh /opt/decom.sh
COPY --chown=spark:spark --from=staging /tmp/spark/examples /opt/spark/examples/
#COPY --chown=spark:spark --from=staging /tmp/spark/kubernetes/tests /opt/spark/kubernetes/tests/
COPY --chown=spark:spark --from=staging /tmp/spark/data /opt/spark/data/
#COPY --chown=spark:spark --from=staging /tmp/spark/python/pyspark /opt/spark/python/pyspark/
#COPY --chown=spark:spark --from=staging /tmp/spark/python/lib /opt/spark/python/lib/
#COPY --chown=spark:spark --from=staging /tmp/spark/R /opt/spark/R/

COPY entrypoint.sh /opt/

ENV SPARK_HOME /opt/spark

WORKDIR /opt/spark/work-dir

USER spark

ENTRYPOINT [ "/opt/entrypoint.sh" ]
