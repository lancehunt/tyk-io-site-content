+++
draft = false
title = "Dashboard Setup"
date = 2014-07-29T10:54:19Z
[menu.sidebar_v1_8]
    parent = "setup"
    weight = -200
+++

Getting the dashboard running is very simple, you will need as a pre-requisite to have installed Tyk, Redis and MongoDB (v2.6+). If you haven't done this yet,
it's probably worth making sure that all these are installed and working prior to installing tyk Dashboard. your Tyk installations does not need to be
configured to any upstream services, we can do all that via the dashboard (in fact, this is recommended, because there is not system to import file-based
configurations at the moment).

[There's a demo setup for vagrant](../../v1.6/setup/vagrant-setup) if you're keen on testing Tyk and tyk dashboard without having to muddle through
installing each service individually, the write-up gives a step-by-step account of how to get every service running.

## Requirements for Tyk Dashboard

**MongoDB version 2.6+** Tyk Dashboard requires mongoDB version 2.6 or higher to be installed, using an older version will work,
but analytics data will not be displayed. Tyk Dashboard uses MongoDB's aggegation framework to generate the analytics charts,
which is only available in version 2.6 and up.

**Redis** We recommend using the latest version of Redis, although no specific version is required.

## Step 1: Make sure Tyk is configured to use installations from MongoDB:

Edit the `/etc/tyk.conf` file so hat it has analytics controllers set up:

    {
        ...
        "use_db_app_configs": true,
        ...
        "enable_analytics": true,
        "analytics_config": {
            "type": "mongo",
            "csv_dir": "/tmp",
            "mongo_url": "mongodb://localhost/tyk_analytics",
            "mongo_db_name": "tyk_analytics",
            "mongo_collection": "tyk_analytics",
            "purge_delay": 10
        }

    }

In the above snippet, unnecessary fields have been cut out, it's important to make sure that you have set up Tyk to monitor analytics (so the dashboard
can display them) and the analytics configuration is set up to point at your Mongo database, for a full run-down of the various parts of the configuration
file you can see more details [here](../../v1.6/setup/configuration/).

## Step 2: Unzip the Tyk Dashboard tarball

First off, we need to extract the tarball, this is pretty straightforward:

    vagrant@precise64:~$ tar -xvzf tyk-dashboard-amd64-v0.9-4.tar.gz
    vagrant@precise64:~$ cd tyk-analytics-v0.9-4

## Step 3 (optional): Place your license file in the `tyk-analytics-*` directory

Make sure that it i called license.dat

## Step 4: Edit the the `tyk_analytics.conf` file

The `tyk_analytics.conf` file is the main setup document for your Tyk Dashboard installation. It has only a few fields that mainly pertain to being
able to connect to Redis, Tyk and MongoDB, it looks like this:

    {
        "listen_port": 3000,
        "tyk_api_config": {
            "Host": "http://localhost",
            "Port": "5000",
            "Secret": "352d20ee67be67f6340b4c0605b044b7"
        },
        "mongo_url": "mongodb://localhost/tyk_analytics",
        "page_size": 10,
        "admin_secret": "12345",
        "redis_port": 6379,
        "redis_host": "localhost",
        "redis_password": "test",
        "force_api_defaults": true,
        "notify_on_change": true,
        "license_owner": "Your Name"
    }

**Note:** The default `tyk_analytics.conf` has the redis password set to `test` if this is a vanilla install, or you are not using
password authentication, please make sure that this field is empty.

## Step 5: Initialise your Tyk Dashboard installation:

Tyk Dashboard enables user roles and API segregation through Tyk via Organisations and Users, each user must belong to an organisation. A user without an Organisation
has access to all on the system, it is recommended to start with one organisation and one user so as to make key creation and API management easier.

In order to initialise and first-run your Tyk Dashboard, simply run it on the command line as follows:

    vagrant@precise64:~/tyk-analytics-v0.9$ ./tyk-analytics --neworg --newuser

You will be presented with a few questions about the Organisation and User to create, **Use quotation marks to enter multi-word names**, if your organisation is called
`John Smith Limited`, then when asked for the Org name, enter `"John Smith Limited"`, as otherwise it will break. f you've done this, simply hit `Ctrl+C` to quit the
app and try again.

If you've created an Organisation, and then gone through the User Creation wizard, Tyk should start up, and you should see that it is listening on the server at the
configured port.

You should now be able to go to http://your-tyk-host.com:PORT/ and see a login screen.

## Step 6: Log in as your new user

You should now be able to log in. From the dashboard, you should be able to create more users, more upstream APIs and view the usage statistics of your
various services, users and keys. If this is a fresh install, you probably won't see any stats until you configure an API and start sending traffic to it.

## Step 7: Running the server

Tyk dashboard is very easy to run, and takes no additional requirements to start up, to start the server simply type the following into your command line:

    vagrant@precise64:~/tyk-analytics-v0.9.4$ ./tyk-analytics


## Congrats, you're all done!

That's all there is to it, once Tyk dashboard is set up, you can use it to manage all of your services..
