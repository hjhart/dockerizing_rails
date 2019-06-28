Okay, let's install webpacker!

Add this to your Gemfile.
```
gem 'webpacker', '~> 4.x'
```

Now run the install command:

```bash
docker-compose run --rm app bundle
```

Now lets' follow the default instructions and run the right rake task.

TODO: Figure out if you want to install yarn as part of the excercise or if you want to include it in the docker image.

```bash
docker-compose run --rm app rails webpacker:install
```

Yarn isn't part of Docker image, so let's add it in the Dockerfile.

```
RUN apk add --update --no-cache \
      bash \
      build-base \
      nodejs \
      sqlite-dev \
      tzdata \
      postgresql-dev
      yarn
```

Now let's rebuild the image:

```
docker-compose build app
```

Now let's try installing webpacker again:
```bash
docker-compose run --rm app rails webpacker:install
```
Webpack can be run one time with bin/webpack or it can be built when changes are made with bin/webpack-dev-server.

Let's add a bin/webpack-dev-server to our docker-compose.yml file.


```
  webpack-dev-server:
    image: your_docker_id/rails_app:v1
    command: ["bin/webpack-dev-server"]
    volumes:
      - ./:/usr/src/app
      - gems:/usr/local/bundle
    environment:
      - NODE_ENV
    ports:
      - 3035:3035
```

And then boot up the webpack dev server

```
docker-compose up -d
```

Let's see how healthy it is:

```
docker-compose logs webpack-dev-server
```

And now if we add a javascript pack tag to the layout `app/views/layouts/application.html.erb`:


```
<%= javascript_pack_tag 'application' %>
```


Copy over the content_security_policy.rb so that rails can load up assets from another host:

cp _examples/content_security_policy.rb config/initializers/content_security_policy.rb
And then navigate to localhost:3000 you can see that a pack is now being compiled by rails and is executed on all pages!
