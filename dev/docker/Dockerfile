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

FROM ubuntu:xenial



# configure locale
RUN apt-get update -qq > /dev/null && apt-get install -qq --yes --no-install-recommends \
    locales && \
    locale-gen en_US.UTF-8
ENV LANG="en_US.UTF-8" \
    LANGUAGE="en_US.UTF-8" \
    LC_ALL="en_US.UTF-8"

# Install necessary dependencies for build/test
RUN apt-get install -qq \
    apt-transport-https \
    curl \
    git \
    libkrb5-dev \
    maven \
    openjdk-8-jdk \
    python-dev \
    python-pip \
    python3-pip \
    software-properties-common \
    vim \
    wget

# R 3.x install - ensure to add the signing key per https://cran.r-project.org/bin/linux/ubuntu/olderreleasesREADME.html
RUN add-apt-repository 'deb https://cloud.r-project.org/bin/linux/ubuntu xenial-cran35/' && \
    apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9 && \
    apt-get update && \
    apt-get -qq install r-base

# Add build dependencies for python2
# - First we upgrade pip because that makes a lot of things better
# - Then we remove the provided version of setuptools and install a different version
# - Then we install additional dependencies
RUN python -m pip install -U "pip < 21.0" && \
	apt-get remove -y python-setuptools && \
	python -m pip install "setuptools < 36" && \
	python -m pip install \
        cloudpickle \
        codecov \
        flake8 \
        flaky \
        "future>=0.15.2" \
        "futures>=3.0.5" \
        pytest \
        pytest-runner \
        requests-kerberos \
        "requests >= 2.10.0" \
        "responses >= 0.5.1"

# Now do the same for python3
RUN python3 -m pip install -U pip

# Expose port
EXPOSE 8998

# Add custom files, set permissions
COPY entrypoint.sh /
RUN chmod +x /entrypoint.sh

WORKDIR /workspace
RUN chmod g+w /workspace

#CMD ["/bin/bash", "/opt/start-spark.sh"]

ENTRYPOINT ["/entrypoint.sh"]
# https://archive.apache.org/dist/spark/spark-2.4.5/spark-2.4.5-bin-hadoop2.7.tgz
