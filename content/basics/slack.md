+++
title = "Slack"
weight = 2
+++

## Overview

There are two basic ways you can run your bot on Slack with _flottbot_:

* **Socket Mode** - see Slack's [Socket Mode Documentation](https://api.slack.com/apis/connections/socket#overview)
* **Events API** - see Slack's [Events API Documentation](https://api.slack.com/apis/connections/events-api)

The basic difference is explained on Slack's [Choosing the right APIs](https://api.slack.com/start/planning/choosing) page:

> With the Events API, you pick the events you’re interested in receiving and Slack will send them to an endpoint you specify via HTTP.

> If you don't wish to expose a public, static HTTP endpoint to communicate with Slack, Socket Mode can help.

For a more detailed differentiation you can check out Slack's ["When should I use the Events API and when should I use Socket Mode or the RTM API?"](https://api.slack.com/faq#answers-to-questions-were-often-asked.__events-api__when-should-i-use-the-events-api-and-when-should-i-use-socket-mode-or-the-rtm-api) FAQ page on the topic.

In short, _Socket Mode_ is great for local development and makes iterating on your bot's features much more convenient. Even beyond local development, it may be all you need to run your bot.

If you plan to eventually run multiple instances behind a load balancer due to your bots' needs, _Events API_ is the way you want to go.

Fortunately, Slack makes it fairly trivial to toggle between the two modes.

## Prepare Files/Folder Structure

Before we start, let's create a quick configuration for your bot. It will consist of a basic **hello** rule.

The typical directory structure for your bot is expected to look like:

```sh
./config/
├── rules/
│   └── hello.yml
└── bot.yml
```

Example **hello** rule to start with:

`config/rules/hello.yml`

```yaml
name: hello
active: true
respond: hello

format_output: "what's up?"

help_text: hello
include_in_help: true
```

We'll assume the above is in place for the remainder of the page and we'll primarily modify `config/bot.yml` going forward.

## Quick Setup

Slack recently released a beta version of [app manifests](https://api.slack.com/reference/manifests) to help create new apps and bots more conveniently.

_Note: it's possible this option doesn't exist for your workspace - continue to [Setup](#setup), if that's the case. You will be able to tell once you start creating a new app as described below._

Below is a prepared manifest that you can utilize to quickly setup your own new app/bot with Socket Mode enabled, cleverly named "my-bot". It's a good starting point even if you eventually end up going down the Events API path. Don't worry, you can customize all the settings that this manifest provides after your bot has been created.

### App Manifest for a Bot Using Socket Mode

```yaml
_metadata:
  major_version: 1
  minor_version: 1
display_information:
  name: my-bot
  description: my bot
  background_color: "#272a30"
features:
  app_home:
    home_tab_enabled: true
    messages_tab_enabled: true
    messages_tab_read_only_enabled: false
  bot_user:
    display_name: my-bot
    always_online: true
oauth_config:
  scopes:
    bot:
      - app_mentions:read
      - channels:history
      - channels:manage
      - channels:read
      - chat:write
      - groups:history
      - groups:read
      - groups:write
      - im:history
      - im:read
      - im:write
      - mpim:history
      - mpim:read
      - mpim:write
      - team:read
      - usergroups:read
      - users:read
      - users:read.email
      - users:write
      - reactions:read
      - reactions:write
settings:
  event_subscriptions:
    bot_events:
      - app_mention
      - member_joined_channel
      - member_left_channel
      - message.channels
      - message.groups
      - message.im
      - message.mpim
  interactivity:
    is_enabled: false
  org_deploy_enabled: false
  socket_mode_enabled: true
  is_hosted: false
```

To use this app manifest, follow these steps:

1. Go to [https://api.slack.com/apps](https://api.slack.com/apps)
1. Hit the "Create New App" button in the top right of the page - a dialog will appear
1. Click on "From an app manifest"
1. On the following screen, pick your workspace and hit "Next"
1. Now review and paste the app manifest above into the text area in the dialog and hit "Next"
1. Here you can review the settings before hitting "Create" (don't worry you can make changes after creating the app). Click on "Create"
1. The dialog will close and you will be returned to your "Basic Information" page.

At this point, your app has been created, but we're missing a couple of tokens that we need for your bot.

### Tokens

On the "Basic Information" page, click on "Install to Workspace" and complete the directions.

Continue to [Socket Mode Tokens](#socket-mode-tokens) to create and capture the necessary tokens to complete the setup.


## Setup

Got access to a Slack workspace? Great. Let's create a new app for your bot. The first few steps will be identical whether you are going with _Socket Mode_ or _Events API_. In the [Events API section](#events-api) we'll go over how to utilize, or switch to, the Events API.

_Tip: there is a quicker way using Slack's new App Manifests feature, see [Quick Setup](#quick-setup) for more information._

### Create Slack App

1. Login to Slack and go to the [Apps page](https://api.slack.com/apps).
1. Click on the green "Create New App" button on the top right of the page. A "Create A Slack App" window should appear.
1. Enter your bot's name in the "App Name" section and select the desired workspace from the drop-down.
1. Click "Create App" at the bottom right corner of the window.
1. You should now be redirected to your bot's "Basic Information" page.

### Add Permissions

Now that your app is created, you will need to configure it. Let's start with permissions (called scopes in Slack):

1. On the left-hand side of the page under "Features" heading, click on "OAuth & Permissions".
1. Scroll down to the "Scopes" section and in the "Bot Token Scopes" sub-section click on "Add an OAuth scope" and add the following scopes:

    * `app_mentions:read`
    * `channels:history`
    * `channels:manage`
    * `channels:read`
    * `chat:write`
    * `groups:history`
    * `groups:read`
    * `groups:write`
    * `im:history`
    * `im:read`
    * `im:write`
    * `mpim:history`
    * `mpim:read`
    * `mpim:write`
    * `team:read`
    * `usergroups:read`
    * `users:read`
    * `users:read.email`
    * `users:write`
    * `reactions:read`
    * `reactions:write`

### Subscribe to Events

Next, let's tell Slack which events your bot should subscribe to:

1. On the left-hand side of the page under the "Features" heading, click on "Event Subscriptions".
1. Click the sliding toggle to turn "Enable Events" to "On".
1. Ignore the "Request URL" for now and scroll down and expand "Subscribe to bot events".
1. Click on "Add Bot User Event" and add the following events:

    * `app_mention`
    * `member_joined_channel`
    * `message.channels`
    * `message.groups`
    * `message.im`
    * `message.mpim`

### Customize Your Bot Name and Display Options

There are a couple of places where you can customize some basics about your bot, such as name and icon:

1. App Home

    * Under "Features" in the left-hand column, click on "App Home".
    * Near the top, click on "Edit" to customize your bot name.
    * You might also want to toggle the "Always Show My Bot as Online" option to be enabled; however, that's up to your preference.

1. Basic Information

    * Under "Settings" in the left-hand column, click on "Basic Information".
    * Towards the bottom of the page, you can customize the "Display Information" section, including setting an App Icon for your bot.

At this point, you're ready to make a call on whether you want to go with the _Socket Mode_ or _Events API_ setup. Let's start with _Socket Mode_.

### Socket Mode Tokens

For Socket Mode, flottbot requires you to supply two tokens: `slack_token` and `slack_app_token`. Here's how to retrieve those tokens from Slack.

#### `slack_token`

1. Scroll back up to the "OAuth Tokens & Redirect URLs" section on [api.slack.com](https://api.slack.com), and click the now green "Install App to Workspace" button (it might say "Reinstall to Workspace").
1. You should be prompted to "Confirm your identity for you Workspace". Click the green "Authorize" button.
1. You should be redirected back to the "OAuth & Permissions" page where you will now see a "OAuth Tokens for Your Workspace" section and your `slack_token` (bot token) which will always begin with **`xoxb-`**.

#### `slack_app_token`

1. In the left-hand menu under "Settings", click on "Socket Mode"
1. In the "Connect using Socket Mode section", slide the toggle for "Enable Socket Mode"
1. Pick and enter a token name in the window that appears. The name you choose does not seem to matter.
1. Click "Generate"
1. In the new window, a token starting with **`xapp-`** will appear. This is your `slack_app_token`.

You will want to set these two tokens in your `/config/bot.yml`. For example:

```yaml
chat_application: slack
slack_token: ${SLACK_TOKEN}
slack_app_token: ${SLACK_APP_TOKEN}
```

_Note: the `${VAR}` syntax expects the values to be set in environment variables with their respective names._

### Run Your Bot (Socket Mode)

Now run your bot using docker with the following command (make sure docker is up and running):

```sh
docker run --rm --name mybot \
  --env SLACK_TOKEN=$SLACK_TOKEN \
  --env SLACK_APP_TOKEN=$SLACK_APP_TOKEN \
  -v "$PWD"/config:/config \
  target/flottbot:latest /flottbot
```

_Note: this assumes that you have the tokens are available as environment variables in your environment at `SLACK_TOKEN` and `SLACK_APP_TOKEN`._

Your bot should now be online in the Slack Workspace you added the integration to.

Head to a channel in Slack and invite your bot. Type `@<your bot name> hello` (replace "your bot name" with the actual name of your bot) and you should get a response of "what's up?").

Congrats!

## Events API

Once you are set up with Socket Mode, you might not need anything else. However, in some circumstances you might decide to switch to use the Events API. The process is fairly straightforward. It will involve the following steps:

1. Configure tokens
1. Setup a public ingress
1. Tweak your `bot.yml`

### Configure tokens

#### `slack_token`

Refer to [Socket Mode](#socket-mode) on how to find your `SLACK_TOKEN`.

#### `slack_signing_secret`

1. See the "Basic Information" page of your App at [api.slack.com](https://api.slack.com)
1. Under the "App Credentials" section, the token is under "Signing Secret". Click on "Show" to display the value

You will want to set these two tokens in your `/config/bot.yml`. For example:

```yaml
chat_application: slack
slack_token: ${SLACK_TOKEN}
slack_signing_secret: ${SLACK_SIGNING_SECRET}
```

_Note: the `${VAR}` syntax expects the values to be set in environment variables with their respective names._

### Setup Your Slack Events Public Ingress

In order for the Slack events API to post back to your bot to notify it that certain events occurred, you'll need to deploy it with a publicly available URL.  Flottbot will by default serve up HTTP endpoints at a custom path you can configure in your `config/bot.yml`.

```yaml
slack_events_callback_path: /slack_events/v1/events
```

If you're using our provided Docker image, it will expose the necessary ports for the HTTP server to accept requests and set up the custom path endpoints to accept Slack event payloads. You will just have to let Slack know where to reach it at.

_Tip: flottbot also exposes a `/event_health` endpoint for checking liveliness._

Before configuring things inside of Slack, you'll want to make sure your bot is running, so Slack can verify the connection to your bot.

At this point, your `config/bot.yml` should look like:

`config/bot.yml`

```yaml
name: mybot
chat_application: slack
slack_token: ${SLACK_TOKEN}
slack_signing_secret: ${SLACK_SIGNING_SECRET}
slack_events_callback_path: /slack_events/v1/events
```

_Note: the `${VAR}` syntax expects the values to be set in environment variables with their respective names._

### Run Your Bot (Events API)

It's beyond this document to go into details on how and where to host your bot so it's publicly accessible.

If your environment allows (many corporate environments don't), [ngrok](https://ngrok.com/) or similar tools ease the pain a little bit by allowing you to run it locally and exposing port(s) publicly via a tunnel.

For sake of simplicity, let's assume that you have your bot running at `https://example.com` for the remainder of this section.

In your bot configuration inside of Slack, ie. [api.slack.com](https://api.slack.com), define the "Request URL" by doing the following:

1. head to the "Events Subscription" page
1. hit the "Enable Events" button to turn it on
1. paste in the URL that your bot is hosted at including the configured `slack_events_callback_path`, in our example's case the full URL would be: `https://example.com/slack_events/v1/events`
1. if everything was done correctly you should get a green check mark.

_Note: you will also have to disable Socket Mode on the "Socket Mode" page, if you had that enabled previously._

Your bot should now be online in the Slack Workspace you added the integration to.

Head to a channel in Slack and invite your bot. Type `@<your botname> hello` (replace "your botname" with the actual name of your bot) and you should get a response of "what's up?").

Congrats!