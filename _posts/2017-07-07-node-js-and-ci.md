---
layout: post
---

## Setting up some CI for simple Node.js project

This tutorial is my "memory card" how to set up some CI/CD features for simple node.js project. When I push changes to [GitHub](http://github.com)  repository, automated tests on [TravisCI](https://travis-ci.org/) are running, after successful tests TravisCI deploys the project on [Heroku](https://heroku.com). 

### Creating project

First we create and set up our project.

1. Create repository on GitHub  and initialize it with readme

2. Clone repository

    ```
    git clone git@github.com:<user>/<repo>.git
    ```

3. Go to the project's folder:

    ```
    cd <working_dir>
    ```

4. Initialize the project:

    ```
    npm init
    ```

5. Install project dependencies:

    ```
    npm install body-parser express mongodb mongoose --save
    ```

6. Install development dependencies:

    ```
    npm install expect mocha nodemon supertest --save-dev
    ```

7. Add some code to create minimal working sample

8. Push to GitHub

    ```
    git push
    ```

### Creating Heroku application

Next, we create [Heroku](https://heroku.com) application. We assume that heroku CLI is installed.

1. Create Heroku application

    ```
    heroku create
    ```

2. Add mongolab addon to use Mongodb:

    ```
    heroku addons:create mongolab:sandbox
    ```

3. Push our repository to Heroku:

    ```
    git push heroku master
    ```

It Works!

### Integration with TravisCI

1. Create file `.travis.yml` in root directory

    ```
    language: node_js 
    node_js:
    - '6'
    services:
    - mongodb
    addons:
      apt: 
        sources: 
        - ubuntu-toolchain-r-test
        packages:
        - g++-4.8    
    ```

2. On TravisCI add repository, enable "Build only if .travis.yml is present" and "Build pull request updates"

3. Push our project with `.travis.yml`

    ```
    git push
    ```

4. If we want, we can add TravisCI badge to readme.md

5. To add [Slack](https://slack.com/) notifications, we need to have our own Slack team. We create channel for our project, add to this channel TravisCI integration and follow the instructions.

6. It's strongly recommended to encript the API keys. We need `travis` gem to do it.

    ```
    gem install travis
    ```

7. Add encrypted key to `.travis.yml`:

    ```
    travis encrypt "OUR_KEY_FROM_CHANNEL_SETTINGS" --add notifications.slack
    ```

8. After key is encrpted and added to `.travis.yml`, we do `git push` and enjoy!

### Enable Heroku notification in Slack

1. In Slack, add Heroku integration. Following the instruction, run the command in project directory:

    ```
    heroku addons:create deployhooks:http --url https://hooks.slack.com/services/XXXXXXXXXXX
    ```

### Enable Github notifications in Slack

1. In Slack, add Github notifications. To do this, add Github integration to Slack channel, authorize to Github, select repository to integrate.

### Auto-deploy to Heroku

Now we can automatically deploy our application to Heroku using TravisCI after successfull build:

1. Execute command in project directory:

    ```
    travis encrypt $(heroku auth:token) --add deploy.api_key
    ```

2. Add to .travis.yml lines (before encrypted Heroku token):

    ```
    provider: heroku
    app: heroku_app_name
    ```

3. Push our project to GitHub only:

    ```
    git push
    ```

All work for us will be done by [GitHub](http://github.com) and [TravisCI](https://travis-ci.org/), we will receive notifications in our Slack channel.

