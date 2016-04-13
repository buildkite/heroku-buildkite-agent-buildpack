# Heroku Buildpack for the Buildkite Agent

This is designed to build an app to be a master agent for buildkite. It includes
a runner script that schedules child jobs in to "heroku run" processes, enabling
you to say use Performance-L dynos in an on-demand auto-scaling environment.
It's built as a buildpack to enable to to manage things independently in your
own code repo, aside from the runner.

## Configuration

The following environment variables are required

* *HEROKU_API_KEY*
* *HEROKU_API_USER*
* *AGENT_COUNT* Buildkite's agent doesn't currently support any concurrency. Because of this, we will spawn up X agents inside the dyno.

If you have a `script/agent_bootstrap` this will be run at slug compile time.
This can be useful for installing runtimes etc. to keep them cached in the slug.

## Use

Create an app using this buildpack.

```
heroku create myapp --buildpack https://github.com/lstoll/heroku-buildkite-agent-buildpack
```

Add other buildpacks your 'build' environment needs to get compiled (optional)

```
heroku buildpacks:add xxxx -a myapp
```

Config set

```
heroku config:set HEROKU_APU_KEY=........ -a myapp
```

Push your code.

Scale agent to 1. Set app default dynos to whatever
size you want. Probably can set the agent to just a normal size.

```
heroku ps:scale agent=1 -a myapp
heroku dyno:type performance-l -a myapp
heroku dyno:type agent=standard-1x -a myapp
```
