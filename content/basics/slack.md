+++
title = "Slack"
weight = 2
+++

## Choose Your Approach

You'll need to install your bot on Slack, but first know that there are two approaches of installing your bot:

1. `App + Bot User` - create a Slack App in the workspace and add the Bot User feature to it.
2. `Bot Integration` - add a `Bot Integration` directly to the workspace.

Each approach has its own set of pros and cons:

### App + Bot User

Recommended if you've already got some experience as a Slack bot owner.

Pros

* Apps offer your bot more features, such as Event Subscriptions, Interactive Components, etc.
  * This means greater customization for your bot
  * More fun
* Your bot will read messages as events, as well as have access to other event subscriptions using the Events API.
  * [Events API](https://api.slack.com/events-api) vs. [RTM](https://api.slack.com/rtm)
* Deploy multiple instances of your bot, since messages will be read/sent as events
  * Load balancing between multiple instances of your bot (good if your bot is really popular).
  * High availability.

Cons

* Takes more time to setup than a simple `Bot Integration`
* Cannot easily run/test locally on machine (due to the Events API)

### Bot Integration

Recommended if you're just starting out. Nice for first-time Slack bot owners.

Pros

* Quick setup
* Good for bots with simple use-cases
* Can run/test bot locally on machine

Cons

* Only one instance of your bot can be deployed because it uses the Slack [RTM](https://api.slack.com/rtm) to read/send messages
* You lose out on App features (not as fun)

### Approach 1: App + Bot User

#### Create A New App

Got access to a Slack workspace? Great. Let's create a new App for your bot.

##### Create Slack App

1. Login to Slack and go to the [Apps page](https://api.slack.com/apps).
2. Click on the green `Create New App` button on the top right of the page. A `Create A Slack App` window should appear.
3. Enter your bot's name under the `App Name` section and select the desired workspace from the dropdown.
4. Click `Create App` at the bottom right corner of the window.
5. You should now be redirected to your App's page.

##### Add Bot User

1. On your App's page, click the `Add features and functionality` dropdown.
2. Click on the `Bots` module. You should be redirected to the `Bot User` page.
3. Click the `Add a Bot User` button
4. Enter your bot's name in the `Display name` and `Default username` field.
    * Call the bot whatever you want!
5. Click on the `Always Show My Bot as Online` toggle to 'On'
6. Click on the green `Add Bot User` button

##### Add Permissions

1. On the lefthand side of the page under the `Features` heading, click on `OAuth & Permissions`.
2. Scroll down to the `Scopes` section and in the `Select Permission Scopes` dropdown, select the `Add a bot user...` option. Click the green `Save Changes` button.
3. Scroll back up to the `OAuth Tokens & Redirect URLs` section, and click the now green `Install App to Workspace` button.
4. You should be prompted to `Confirm your identity for you Workspace`. Click the green `Authorize` button.
5. You should be redirected back to the `OAuth & Permissions` page where you will now see a `Tokens for Your Workspace` section and your bot token which will always begin with `xoxb`.

You should be able to see your bot online in the workspace you installed it. On the bottom left side panel, you should see the `Apps` heading. If you click on the `+` button, you should be able to search and select your bot. Your bot doesn't do anything right now, so we'll need to add functionality to it.

##### Setup Your Slack Events Public Ingress

In order for the Slack events API to post back to your bot to notify it that certain events occur, you'll need to deploy it to a publicly available URL. Flottbot will by default serve up http endpoints at custom paths you can configure in your ```config/bot.yml```.

```yaml
slack_events_callback_path: ${SLACK_EVENTS_CALLBACK_PATH} # EDIT  ${SLACK_EVENTS_CALLBACK_PATH} (e.g. /slack_events/v1/events)
slack_interactions_callback_path: ${SLACK_INTERACTIONS_CALLBACK_PATH} # EDIT ${SLACK_INTERACTIONS_CALLBACK_PATH} (e.g. /slack_events/v1/interactions)
```

If you're using our provided Docker image, it will expose the necessary ports for the http server to accept requests and set up the custom path endpoints to accept slack event payloads.

In your bot configuration inside of Slack migrate to the `Events Subscription` section. Hit the *Enable Events* button to turn it on. Paste in the URL that your bot is hosted at including the new path that now accepts payloads from the Slack events API. See our section on Deployment for deploying your custom Flottbot docker container on Heroku. If using the examples listed above with the URL https://example.com you would paste in https://example.com/https://stage-api.target.com/slack_events/v1/interactions to the Request URL field. Make sure that you get the green check mark when saving the page. Then add the following Bot Subscription Events:

```
Event Name Description
link_shared
A message was posted containing one or more links relevant to your application

member_joined_channel
A user joined a public or private channel

member_left_channel
A user left a public or private channel

message.app_home
A user sent a message to your Slack app

message.channels
A message was posted to a channel

message.groups
A message was posted to a private channel

message.im
A message was posted in a direct message channel

message.mpim
A message was posted in a multiparty direct message channel
```

#### Setup Your Bot Project

Create a directory structure to house your bot's config:

```sh
mkdir config
cd config
mkdir rules
```

Create the following files in the specified paths:

`config/bot.yml`

```yaml
name: mybot
chat_application: slack
slack_token: ${SLACK_TOKEN}
slack_verification: ${SLACK_VERIFICATION_TOKEN}
slack_workspace_token: ${SLACK_WORKSPACE_TOKEN}
```

**Note:** Your Slack bot token will begin with "xoxb" and your workspace token with "xoxp"_

How to find these Slack tokens:

* `${SLACK_TOKEN}`
    1. See the **OAuth & Permissions** page of your App.
    2. Under the **Tokens for Your Workspace** section, the token is under **Bot User OAuth Access Token**.
* `${SLACK_WORKSPACE_TOKEN}`
    1. See the **OAuth & Permissions** page of your App.
    2. Under the **Tokens for Your Workspace** section, the token is under **OAuth Access Token**.
* `${SLACK_VERIFICATION_TOKEN}`
    1. See the **Basic Information** page of your App.
    2. Under the **App Credentials** section, the token is under **Verification Token**.

Cool. Save those tokens somewhere safe. Commit these changes and we'll work on running your bot.

### 4. Running Your Bot

So as mentioned in the above pros/cons for the `App + Bot User` approach, you won't be able to run/test your bot in Slack from your local machine. This is also why many choose to go the `Bot Integration` path first.

### Approach 2: Bot Integration

This is the quick and easy way of getting your bot up and running in Slack ASAP.

#### Install Your Bot Integration

Got access to a Slack workspace? Great. Let's add your integration:

1. Go to the your Workspace's [App Directory](https://api.slack.com/apps), search for 'Bots' and click the option _`Bots Connect a bot to the Slack Real Time Messaging API`_.
2. Click on the green `Add Configuration` button on the left.
3. Enter your chosen username for your bot and click on the green `Add bot integration`.
4. You should now be able to access your bot's `Integration Settings` where you will find your bot's `API Token`, which will look like `xoxb-xxxxxxxxxxxx-xxxxxxxxxxxxxxxxxxxxxxxx`.

Hooray! Now let's run your bot in your workspace.

#### Setup Your Bot Project

Edit the following lines in `/config/bot.yml` to look like:

```yaml
name: mybot
chat_application: slack
slack_token: ${SLACK_TOKEN}
```

Now, you'll need to export your Slack bot token as an environment variable wherver your bot runs:

```sh
export SLACK_TOKEN=xoxb-xxxxxxxxxxxx-xxxxxxxxxxxxxxxxxxxxxxxx
```

**Note:** Your Slack bot token should begin with "xoxb"

Set up a simple hello rule.

`config/rules/hello.yml`

```yaml
# metadata
name: hello
active: true
respond: hello
format_output: "what's up?" # message to send to your user when they say hello
direct_message_only: false
help_text: hello
include_in_help: true
```

### Run Your Bot

Now run your bot using docker with the following command (make sure docker is up and running):

```sh
docker run --rm --name mybot --env SLACK_TOKEN=$SLACK_TOKEN -v "$PWD"/config:/config target/flottbot:latest /flottbot
```

Your bot should now be online in the Slack Workspace you added the integration to.

1. On the bottom left side panel, you should see the `Apps` heading. Click on the circular `+` button.
2. In the `Browse Apps` search bar, enter your bot's username. Your bot should appear as a result. Click on it. You should now see your bot online under the `Apps` heading of the side panel.
3. Message it `hello` and it should respond `what's up?`
