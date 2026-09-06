FROM public.ecr.aws/docker/library/python:3.10-alpine

ENV FLASK_RUN_HOST=0.0.0.0

WORKDIR /

RUN adduser -u 1000 -D app

RUN apk update && \
  apk add \
  pcre \
  pcre-dev \
  build-base \
  gcc \
  musl-dev \
  linux-headers \
  python3-dev \
  openssl \
  libffi-dev \
  && pip install pipenv

# GOTCHA FIX: GCC 14 treats uwsgi's old pointer-type code as a hard error
ENV CFLAGS="-Wno-error=incompatible-pointer-types"

RUN mkdir -p /var/www/app && mkdir /app
COPY Pipfile* /app
RUN cd /app && pipenv install --system --deploy
COPY . /app
CMD uwsgi --http :5000 --master --enable-threads -s /var/www/app/app.sock --manage-script-name --mount /=app:app