#Using Fluentd to Collect Tweets into MongoDB

##Overview

MongoDB is an awesome NoSQL database that lets you get started very quickly.

Fluentd is an awesome data collector that lets you collect data from various data sources and stream them into different systems.

This is an example implementation of collecting Tweets into MongoDB using Fluentd. It's been set up to run on [Cloud Foundry](https://run.pivotal.io/).

More specifically, it "tails" the user stream of the given Twitter account and stream the tweets into MongoDB for further analysis.

The main differences between this repo and https://github.com/kiyoto/heroku-fluentd-twitter-mongo are:

- the input of application configurable parameters via environment variables
- 3rd party service (mongolab) binding to the application

##Setup

###Prerequisites

- Cloud Foundry account w/ MongoLab service
- Ruby (tested with v2.0.0)
- Twitter developer account (consumer key/secret, OAuth token/secret)
- git

###Clone this repo

To get started, clone this repo

```
$ git clone https://github.com/lcddave/cf-fluentd-twitter-mongo.git
Cloning into 'cf-fluentd-twitter-mongo'...
remote: Counting objects: 7, done.
remote: Compressing objects: 100% (5/5), done.
remote: Total 7 (delta 0), reused 7 (delta 0)

```

###Add Mongolab as an addon

If you have a MongoDB instance running already, you can skip this step. Otherwise, you can create a MongoDB instance with Mongolab addon.

```
cf cs mongolab sandbox demo-mongo
```

###Configure Fluentd

This example automatically configures Fluentd via environment variables set in the manifest.yml. Enter the values for:

```
  consumer_key        YOUR_CONSUMER_KEY # Required
  consumer_secret     YOUR_CONSUMER_SECRET # Required
  oauth_token         YOUR_OAUTH_TOKEN # Required
  oauth_token_secret  YOUR_OAUTH_TOKEN_SECRET # Required
```

You need to update it with your Twitter consumer key/secret, Twitter OAuth token/secret (see [here](https://dev.twitter.com/discussions/631) for how to get those)

### Push to Cloud Foundry

```
$ cf push 
```

Now your app should be live!

### Check that data is coming into MongoDB

If you are using Mongolab, you can use their browser-based admin tool. In cloud foundry this is accessible via the [Developer Console](https://console.run.pivotal.io).

The Tweets should be collected in the collection `tweets`. Here is a screenshot

<img src="https://dl.dropboxusercontent.com/u/63890117/images/mongolab-fluentd.png"/>

### What's Next?

- [Fluentd website](http://fluentd.org)
- [Fluentd documentation](https://docs.fluentd.org)

Fluentd is sponsored by [Treasure Data](http://www.treasuredata.com)
