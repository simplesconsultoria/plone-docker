FROM python:3.8-slim-buster as base
FROM base as builder

ENV PLONE_VERSION=5.2.4

RUN mkdir /wheelhouse

RUN apt-get update \
    && buildDeps="dpkg-dev gcc libbz2-dev libc6-dev libffi-dev libjpeg62-turbo-dev libldap2-dev libopenjp2-7-dev libpcre3-dev libpq-dev libsasl2-dev libssl-dev libtiff5-dev libxml2-dev libxslt1-dev wget zlib1g-dev python3-dev build-essential" \
    && apt-get install -y --no-install-recommends $buildDeps\
    && rm -rf /var/lib/apt/lists/* /usr/share/doc

RUN pip install "pip==19.2.3" && \
    pip wheel Plone Paste -c https://dist.plone.org/release/$PLONE_VERSION/constraints.txt --wheel-dir=/wheelhouse

FROM base

LABEL maintainer="Simples Consultoria <dev@simplesconsultoria.com.br>" \
      org.label-schema.name="plone" \
      org.label-schema.description="Plone base image." \
      org.label-schema.vendor="Simples Consultoria" \
      org.label-schema.docker.cmd="docker run -d -p 8080:8080 simplesconsultoria/plone:5.2.4"

COPY --from=builder /wheelhouse /wheelhouse

RUN useradd --system -m -d /app -U -u 500 plone \
    && runDeps="git libjpeg62 libopenjp2-7 libpq5 libtiff5 libxml2 libxslt1.1 lynx netcat poppler-utils rsync wv" \
    && apt-get update \
    && apt-get install -y --no-install-recommends $runDeps \
    && rm -rf /var/lib/apt/lists/* /usr/share/doc \
    && mkdir -p /data/filestorage /data/blobstorage /data/log /data/cache \
    && chown -R plone:plone /data

WORKDIR /app
USER plone
COPY skeleton/inituser /app/inituser

RUN python -m venv . \
    && ./bin/pip install --force-reinstall --no-index --no-deps /wheelhouse/* \
    && find . \( -type f -a -name '*.pyc' -o -name '*.pyo' \) -exec rm -rf '{}' +

COPY skeleton/etc /app/etc
RUN ln -s /data var

EXPOSE 8080
VOLUME /data

HEALTHCHECK --interval=1m --timeout=5s --start-period=30s CMD nc -z -w5 127.0.0.1 8080 || exit 1

CMD ["/app/bin/runwsgi", "-v", "etc/zope.ini"]
