---
layout: post
title: Running Jekyll with Docker Compose
subtitle: It is one thing to mortify curiosity, another to conquer it
image: /img/jekyll_software.png
bigimg: /img/jekyll_and_hyde_movie.jpg
---

[Jekyll](https://jekyllrb.com/) is a simple, blog-aware, static site generator. Unfortunately it is less than simple to run locally. You need to [install](https://jekyllrb.com/docs/) the correct versions of ruby, ruby gems, GCC, make, and the jekyll gem. Alternatively you can run your site locally using the [Jekyll Docker image](https://hub.docker.com/r/jekyll/jekyll/). In this post I will show you the most efficient way that I have found to do that.

#### Prerequisites
* [Docker](https://www.docker.com/) installed on your machine
* Basic knowledge of [docker containers](https://www.docker.com/resources/what-container)

## Setup
The Jekyll image runs on alpine linux and adds layers with all the dependencies we should need for local development.

We are going to create a simple blog in the setup, but there is no reason why you shouldn't be able to run this using a Jekyll site you have already setup.

### 1. Create our development directory
Create a directory called **blog** and change directories into it using your favorite terminal. The directory can be where ever you want. This is how I made mine:

```shell
mkdir -p ~/dev/blog && cd ~/dev/blog
```

### 2. Creating our Docker Compose file
Create a file called `docker-compose.yml` in our blog directory with the following contents:

```yaml
version: "3.7"

services:
  jekyll_site:
    image: jekyll/jekyll:4.0
    command: ["jekyll", "serve", "--watch", "--draft"]
    ports:
      - "4000:4000"
    volumes:
     - type: bind
       source: .
       target: /srv/jekyll
```

For those unfamiliar with [Docker Compose](https://docs.docker.com/compose/) we are creating a file that configures the services we want to run. The equivalent of this as a `docker container` command would be something like:
```shell
docker container run -v $PWD:/srv/jekyll -p 4000:4000 --name jekyll_site jekyll/jekyll:4.0 jekyll serve --watch --draft
```

### 3. Generate our Blog

{: .box-warning}
Skip this step if you are running against an existing Jekyll site.

We are going to use our Docker Compose file to generate our blog by running `jekyll new --force .` against the container it creates which will generate a new Jekyll site. The `--force` is to force Jekyll to create the site even though we already have a file in this directory. To do this we are going to use the docker compose [run](https://docs.docker.com/compose/reference/run/) command which will create a container based on the compose file and run a command against it.
```shell
docker-compose run jekyll_site jekyll new --force .
```

### 4. Test it
Run `docker-compose up` and wait until you see `Server running... press ctrl-c to stop.`. Then you should be able to go to [http://localhost:4000](http://localhost:4000) to see your new blog. If you make a change to the `index.markdown` file and refresh your browser the change should be reflected on your site!

{: .box-note}
The first time you run this it will take quite awhile because docker is downloading a lot of dependencies, but it will be much faster the next time.

## Development Tips

### Happy Path
If you are just looking to start and stop your server you can just use:
```shell
docker-compose up
```
and hit `ctrl-c` when you are done.

### Running detached
I prefer running detached because it frees up my terminal to run additional commands against the container.

**Start the detached container**
```shell
docker-compose up -d
```

{: .box-note}
You could also run `docker-compose start` instead if your container is already created.

**Follow logs of a container**
```shell
docker-compose logs -f jekyll_site
```
**Stop the container**
```shell
docker-compose stop
```

### Running commands against the container
You can execute commands against a running container by using the `docker-compose exec` command. For example if you wanted to see what directory the minima gem was in you would run `bundle show minima`. Combine those two commands and you end up with
```shell
docker-compose exec jekyll_site bundle show minima
```

### Direct access to the container
To access the container directly you can execute the command `sh` or `bash` which will give you direct access to the container.
```shell
docker-compose exec jekyll_site bash
```

### Clean up
If you don't plan on doing any work on your Jekyll site for awhile you can remove the containers, networks, images, and volumes you created with your Docker Compose by running:

```shell
docker-compose down
```

## Conclusion
Hopefully this helps you as much as it helped me. As someone who is new to Jekyll it has made my life considerably easier because I know the first step of learning a new technology is already done.
