# Bundler image

## Dockerfile_bundler
```
FROM ruby:2.7

RUN apt-get update && apt-get install -y nodejs npm --no-install-recommends && rm -rf /var/lib/apt/lists/*

RUN npm install --global yarn

RUN apt-get update && apt-get install -y postgresql-client sqlite3 --no-install-recommends && rm -rf /var/lib/apt/lists/*

RUN gem install rails rspec bundler

WORKDIR /root	

CMD ["bash"]
```
## Run the bunlder image

> Docker build -t jms:rails_builder .


Then run a container only for project creation
```
docker run -v ${PWD}:/root/ jms:rails_builder rails new <<app_name_here>>
```
...look this is not interactive because of its only purpose

example:
> docker run -v ${PWD}:/root/ jms:rails_builder rails new my_app --skip-turbolinks


# Server Image

## 🧙🏼‍♂️ SERVER

Build an image for the service 

#Dockerfile
```
FROM jms:rails_builder

COPY ./my_app /root/my_app

WORKDIR /root/my_app

RUN bundle install

EXPOSE 3000

CMD ["bash"]
```

> Docker build -t jms:rails_server .

```
docker run -v ${PWD}/<<APP-NAME>>:/root/<<APP_NAME>>/ -p "3000:3000" jms:rails_server
```
example.
> docker run -it -v ${PWD}/my_app:/root/my_app/ -p "3000:3000" jms:rails_server


> docker run -v ${PWD}:root/ jms:rails_builder rails new <<app_name_here>>

> docker exec -it <<containerhash>> bash

# docker-compose.yml (standalone container)
  ```
  web:
  image: jms:rails_server
  command: "rails server -b 0.0.0.0"
  restart: always
  ports:
    - "80:3000"
  volumes:
    - ./my_app:/root/my_app
  ```
# docker-compose.yml (behind reverse proxy)
  ```
  www:
    image: jms-ruby:rails_server
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
