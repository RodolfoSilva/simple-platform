# Simple Platform

Docker Machine + Ngninx + Prometheus + Grafana to provision on a single host

Maybe you don't need a self-managed kubernetes with 3rd party monitoring solutions, maybe a single docker instance running is enough to hold many of your pet projects

## Part 1: provisioning

1. Create an account somewhere where you can spin up cheap instances (like on [scaleway](https://cloud.scaleway.com/) or [digitalocean](https://www.digitalocean.com/))

1. Install docker machine if you don't already have it (try docker-machine in your bash)

    https://docs.docker.com/machine/install-machine/

1. Provision docker machine on the instance following this guide:

    https://docs.docker.com/machine/get-started-cloud/

1. Point your docker to the recently provisioned machine

    ```bash
    eval $(docker-machine env your-machine-name)
    ```

1. Clone this repo and put everything up

    ```bash
    docker-compose build
    docker-compose up -d
    ```

## Part 2: running apps

Now you can go to any other of your projects which have a docker file and simply run them on this machine as well, you can follow the example under example-app

```bash
cd example-app
docker-compose build
docker-compose up -d
```

If everything worked, go to your server ip address and you should see this:

### It works!

The way it works is by having all containers sharing the same host network, which means that you need to make them run on different ports to not conflict, then you can change nginx/nginx.conf to route traffic corretly to multiple apps according to the domain

## Part 3: monitoring

Go to http://yourserver/metrics you should see metrics for our example-app, including one named user_clicks. You can see the code used to generate it on example-app/index.js

Now go to http://yourserver:9090, you will see the Prometheus dashboard, there you can query the metrics, you can add more apps in prometheus/prometheus.yml

For building dashboards, you can go to grafana, on http://yourserver:3000, sing in with admin/admin, set up a new password, choose prometheus as data source with default options and localhost:9090, and get started.

You can create a dashboard with the metric `user_clicks` or `process_resident_memory_bytes` for example

For error tracking there are actually good SaaS with free plans available for small projects on [sentry.io](https://sentry.io) or [rollbar.com](https://rollbar.com)

## Part 4: security

You should probably block the ports other then 80 or 443 in your host, and add a password to prometheus