FROM		alpine:3.4
MAINTAINER	Mark Meyer <mark@ofosos.org>

ENV PYTHON_VERSION=2.7.12-r0
ENV PY_PIP_VERSION=8.1.2-r0
ENV SUPERVISOR_VERSION=3.3.0
ENV PYCURL_VERSION=7.19.0-r0
ENV SETUPTOOLS_VERSION=20.8.0-r0
ENV SHINKEN_VERSION=2.4.3
ENV PLUGINS_VERSION=2.1.2-r0

RUN apk update
RUN apk add -u python=$PYTHON_VERSION py-pip=$PY_PIP_VERSION py-curl=$PYCURL_VERSION py-setuptools=$SETUPTOOLS_VERSION
RUN apk add monitoring-plugins=$PLUGINS_VERSION
RUN pip install supervisor==$SUPERVISOR_VERSION

RUN adduser -D shinken

RUN pip install shinken==$SHINKEN_VERSION

RUN su - shinken -c 'shinken --init'
RUN su - shinken -c 'shinken install livestatus'

# paths, plugins & content config

COPY paths.cfg /etc/shinken/resource.d/paths.cfg
COPY generated.cfg /etc/shinken/hosts/generated.cfg
COPY check_dummy.cfg /etc/shinken/commands/check_dummy.cfg

# mongo retention & logs

RUN pip install pymongo==3.2.2

RUN su - shinken -c 'shinken install retention-mongodb'
RUN su - shinken -c 'shinken install logstore-mongodb'

COPY mod-retention-mongodb.py /var/lib/shinken/modules/retention-mongodb/module.py
COPY mod-logstore-mongodb.py /var/lib/shinken/modules/logstore-mongodb/modules.py

COPY logstore_mongodb.cfg /etc/shinken/modules/
COPY retention-mongodb.cfg /etc/shinken/modules/

# shinken & livestatus configs

COPY broker-master.cfg /etc/shinken/brokers/broker-master.cfg
COPY scheduler-master.cfg /etc/shinken/schedulers/scheduler-master.cfg
COPY livestatus.cfg /etc/shinken/modules/livestatus.cfg

# supervisord

COPY supervisord.conf /etc/supervisord.conf
RUN mkdir /var/log/supervisord

EXPOSE 50000

VOLUME /etc/shinken

ENTRYPOINT ["supervisord", "--nodaemon", "--configuration", "/etc/supervisord.conf"]
