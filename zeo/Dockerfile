FROM python:3.8-slim-buster as base
FROM base as builder

ENV ZEO_VERSION=5.2.2

RUN mkdir /wheelhouse

RUN apt-get update \
    && buildDeps="build-essential python3-dev" \
    && apt-get install -y --no-install-recommends $buildDeps\
    && rm -rf /var/lib/apt/lists/* /usr/share/doc

RUN pip wheel "zeo==$ZEO_VERSION" --wheel-dir=/wheelhouse

FROM base

LABEL maintainer="Simples Consultoria <dev@simplesconsultoria.com.br>" \
      org.label-schema.name="zeo" \
      org.label-schema.description="ZEO (ZODB) Server." \
      org.label-schema.vendor="Simples Consultoria" \
      org.label-schema.docker.cmd="docker run -d -p 8100:8100 simplesconsultoria/zeo:5.2.2"

COPY --from=builder /wheelhouse /wheelhouse

RUN useradd --system -m -d /app -U -u 500 plone \
    && runDeps="git" \
    && apt-get update \
    && apt-get install -y --no-install-recommends $runDeps \
    && rm -rf /var/lib/apt/lists/* /usr/share/doc \
    && mkdir -p /data/filestorage /data/blobstorage /data/log /data/cache \
    && chown -R plone:plone /data

WORKDIR /app
USER plone

RUN python -m venv . \
    && ./bin/pip install --force-reinstall --no-index --no-deps /wheelhouse/* \
    && find . \( -type f -a -name '*.pyc' -o -name '*.pyo' \) -exec rm -rf '{}' +

COPY skeleton/etc /app/etc
RUN ln -s /data var

EXPOSE 8100
VOLUME /data

CMD ["/app/bin/runzeo", "-C", "etc/zeo.conf"]
