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

TODO: Confirm with Julian that we should install yarn as part of the exercise.

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

Webpacker will either compile on-demand with the rails server in development, or you can run a separate process to compile a bit faster. 

Let's add a bin/webpack-dev-server to our docker-compose.yml file.


```
  webpack:
    image: your_docker_id/rails_app:v1
    command: ["bin/webpack-dev-server"]
    volumes:
      - ./:/usr/src/app
      - gems:/usr/local/bundle
    environment:
      - WEBPACKER_DEV_SERVER_HOST=0.0.0.0
      - NODE_ENV
    ports:
      - 3035:3035
```

And add an environment variable to point to the correct host to app and web:

```
    environment:
      - WEBPACKER_DEV_SERVER_HOST=webpack
```

You can also copy it from `_examples/docker-compose.yml.with_webpacker`

And then boot up the webpack dev server

```
docker-compose up
```

And let's watch the logs, because things happen a bit slower

Let's see how healthy it is:

```
docker-compose logs webpack
```

And now if we add a javascript pack tag to the layout `app/views/layouts/application.html.erb`:


```
<%= javascript_pack_tag 'application' %>
```

Copy over the content_security_policy.rb so that rails can load up assets from another host:

```
cp _examples/content_security_policy.rb config/initializers/content_security_policy.rb
```
And then navigate to localhost:3000 you can see that a pack is now being compiled by rails and is executed on all pages!
