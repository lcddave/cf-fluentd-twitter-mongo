#Using Fluentd to Collect Tweets into MongoDB

##Overview

MongoDB is an awesome NoSQL database that lets you get started very quickly.

Fluentd is an awesome data collector that lets you collect data from various data sources and stream them into different systems.

This is an example implementation of collecting Tweets into MongoDB using Fluentd. It's been set up to run on Heroku.

More specifically, it "tails" the user stream of the given Twitter account and stream the tweets into MongoDB for further analysis.

##Setup

###Prerequisites

- Heroku account + [toolbelt](https://toolbelt.heroku.com/).
- Ruby (tested with v2.0.0)
- Twitter developer account (consumer key/secret, OAuth token/secret)
- git

###Clone this repo

To get started, clone this repo

```
$ git clone https://github.com/kiyoto/heroku-fluentd-twitter-mongo.git
Cloning into 'heroku-fluentd-twitter-mongo'...
remote: Counting objects: 7, done.
remote: Compressing objects: 100% (5/5), done.
remote: Total 7 (delta 0), reused 7 (delta 0)

```

###Create a Heroku app

Switch into the repo and create a heroku app for this

```
$ cd heroku-fluentd-twitter-mongo
$ heroku apps:create
Creating lit-island-1813... done, stack is cedar
http://lit-island-1813.herokuapp.com/ | git@heroku.com:lit-island-1813.git
Git remote heroku added
```

###Add Mongolab as an addon

If you have a MongoDB instance running already, you can skip this step. Otherwise, you can create a MongoDB instance with Mongolab addon.

```
$ heroku addons:add mongolab
Adding mongolab on lit-island-1813... done, v3 (free)
Welcome to MongoLab.  Your new subscription is ready for use.  Please consult the MongoLab Add-on Admin UI for more information and useful management tools.
Use `heroku addons:docs mongolab` to view documentation.
```

Now, get the user name, password, host, port and database name:

```
$ heroku config | grep ^MONGOLAB
MONGOLAB_URI: mongodb://heroku_app23902138:8tf7vuikg01f5tj2ubl63jfbnr@ds035147.mongolab.com:35147/heroku_app23902138
```

In the above example,

- user name is heroku_app23902138
- password is 8tf7vuikg01f5tj2ubl63jfbnr
- host is ds035147.mongolab.com
- port is 35147
- database name is heroku_app23902138

###Configure Fluentd

The configuration file is located at `conf/fluent.conf` and it looks like this:

```
<source>
  type twitter
  consumer_key        YOUR_CONSUMER_KEY # Required
  consumer_secret     YOUR_CONSUMER_SECRET # Required
  oauth_token         YOUR_OAUTH_TOKEN # Required
  oauth_token_secret  YOUR_OAUTH_TOKEN_SECRET # Required
  tag                 input.twitter.mongo  # Required
  timeline            userstream # Required (sampling or userstream)
</source>

<match input.twitter.mongo>
  type copy
  <store>
    type stdout # for debugging
  </store>
  <store>
    type mongo
    host YOUR_MONGO_HOST
    port YOUR_MONGO_PORT
    database YOUR_MONGO_DB_NAME
    collection tweets 
    user YOUR_USER
    password YOUR_MONGO_PASSWORD
    flush_interval 5s #for demo. in real life, you want to buffer more
    capped
    capped_size 100m
  </store>
</match> 
```

You need to update it with your Twitter consumer key/secret, Twitter OAuth token/secret (see [here](https://dev.twitter.com/discussions/631) for how to get those)

Use your favorite editor to update all the fields for both MongoDB and Twitter.

###Commit the change and push to Heroku

Now, commit the change with

```
$ git commit conf/fluent.conf -m 'update the configuration'
```

and push to Heroku.

```
$ git push heroku master
```

Now your app should be live!

###Check that data is coming into MongoDB

If you are using Mongolab, you can use their browser-based admin tool:

```
$ heroku addons:open mongolab
```

The Tweets should be collected in the collection `tweets`. Here is a screenshot

<img src="https://dl.dropboxusercontent.com/u/63890117/images/mongolab-fluentd.png"/>

###What's Next?

- [Fluentd website](http://fluentd.org)
- [Fluentd documentation](https://docs.fluentd.org)

Fluentd is sponsored by [Treasure Data](http://www.treasuredata.com)
