![](https://img.shields.io/badge/Tools-Docker-informational?style=flat&logo=docker&logoColor=white&color=2bbc8a)
![](https://img.shields.io/badge/Code-Ruby-informational?style=flat&logo=ruby&logoColor=white&color=2bbc8a)

# 👷🏼‍♂️ BUILDER IMAGE

## Dockerfile_builder
```
FROM ruby:2.7

ENV RAILS_VERSION 'rails -v 5.2.6'

RUN apt-get update && apt-get install -y nodejs npm --no-install-recommends \
        && rm -rf /var/lib/apt/lists/* \
        && npm install --global yarn \
        && apt-get update \
        && apt-get install -y postgresql-client sqlite3 --no-install-recommends \
        && rm -rf /var/lib/apt/lists/* \
        && gem install $RAILS_VERSION \
        && gem install rspec bundler

```
## Build the builder image

> Docker build -t jms:rails -f Dockerfile_builder .

## Create your new project
Then run a temporary container only for project creation
```
docker run -v ${PWD}/src:/src/ jms:rails_builder rails new src
```
...Heads up, this is not interactive because of its only purpose


# ✨ SERVER DOCKERFILE

#Dockerfile
```
FROM jms:rails_builder

WORKDIR /src

COPY src .

RUN bundle install

EXPOSE 3000

CMD ["bash"]

```

# RUN YOUR APP

## docker-compose.yml (standalone container)
  ```
  web:
  build: .
  command: rails server -b 0.0.0.0
  restart: always
  ports:
    - "80:3000"
  volumes:
    - ./my_app:/root/my_app
  ```
## docker-compose.yml (behind reverse proxy)
  ```
  www:
    build: .
    restart: always
    command: rails s -b 0.0.0.0
    expose:
      - "80"
    volumes:
      - ./app:/root/app/:rw
      - "/etc/timezone:/etc/timezone:ro"
      - "/etc/localtime:/etc/localtime:ro"
    environment:
      - VIRTUAL_HOST=${SITE_SUBDOMAIN}.${SITE_MAIN_DOMAIN}, www.${SITE_SUBDOMAIN}.${SITE_MAIN_DOMAIN}
      - LETSENCRYPT_HOST=${SITE_SUBDOMAIN}.${SITE_MAIN_DOMAIN}, www.${SITE_SUBDOMAIN}.${SITE_MAIN_DOMAIN}
      - LETSENCRYPT_EMAIL=${SITE_ADMIN_EMAIL}
      - VIRTUAL_PORT=3000
```
