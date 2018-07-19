+++
title = "Customizing Your Bot"
weight = 4
+++

## Customizing Your Bot

`config/bot.yml` will house the configurations specific to your bot. The below config is for a bot named **mybot**.

```yaml
name: mybot
# chat applications
chat_application: slack

# Slack chat application specific fields.
#required
slack_token: ${SLACK_TOKEN}
# optional
slack_verification_token: ${SLACK_VERIFICATION_TOKEN}
slack_workspace_token: ${SLACK_WORKSPACE_TOKEN}

# Discord chat application specific fields.
# required
discord_token: ${DISCORD_TOKEN}

# system
cli: true
# true: enables ability to turn on CLI mode.
# false: disables ability to turn on CLI mode.

scheduler: false
# true: enables rule scheduling
# false: disables rule scheduling

debug: true
# true: enable logging to console
# false: disable logging

log_json: false
# true: logs will be in JSON format
# false: logs will be in plain text

metrics: true
# true:  enable metrics collection through prometheus
# false: disable metrics collection through prometheus

custom_help_text: "Sorry, I didn't get that. Try sending me one of the following commands: \n `cats` \n `today`"
# set this field to set custom fallback help text for your bot, if it is unset it will send a generic help message with rules and their usage.
```

* **name** - the username of your bot. It should be set as the name you provided in your Bot integration in Slack. This is just used for logging purposes.
* **chat_application** (required _string_) - denotes the chat application we're using, so far we only support [Slack](slack.com) and [Discord](https://discordapp.com/).
* **slack_token** (required _string_) - is a placeholder that the flottbot binary will parse to read in the `SLACK_TOKEN` environment variable when the bot runs. This token can be found on the bot user configuration page and usually starts with `xoxb`
* **slack_verification_token** (optional _string_) - include this if you want your bot to have Slack `Events Subscriptions` and `Interactive Components`.
    * Set up your integration as a _Custom Slack Application_, add the _Bot_ integration to the app and you will receive a token for your bot in the _"Bot User OAuth Access Token"_ section and a token for your workspace in the _"OAuth Access Token"_ section.
* **slack_workspace_token** (optional _string_) include this if you want to include certain features, e.g. user groups, link unfurling, etc. This token can be found in the Oauth configuration page of the bot and usually starts with `xoxp`.
* **discord_token** (optional _string_) is a placeholder that the flottbot binary will parse to read in the `DISCORD_TOKEN` environment variable when the bot runs. You will likely set that variable using a Kubernetes secret.
* **cli** (optional _boolean_) - if this flag is set to true, the bot can be interacted in command line / terminal (useful for testing rules).
* **scheduler** (optional _boolean_) - if this flag is set to true, the bot will enable scheduled messages to be sent to specific channels/users.
* **debug** (optional _boolean_) - if this flag is set to true, the bot to log with more verbosity.
* **log_json** (optional _boolean_) - if this flag is set to true, the bot will log in JSON format.
* **metrics** (optional _string_) - if this flag is set to true, the bot will expose metrics on rule executions on a local prometheus server endpoint.
* **custom_help_text** (optional _string_) - this is a global help text. Caution, if this field is set it will override all `help_text` fields set in your rules.
