+++
title = "Anatomy of a Rule"
weight = 1
+++

A standard rule yaml file will look like this:

```yaml
# metadata
name: echo #name of your rule
active: true # activate rule

# trigger & arguments. you will use of of the below options per rule, not all 3! (respond, hear, schedule)

respond: echo # text match to trigger this rule to take action, bot is looking for the word "echo".
args: # this is an array of args to pass in after a match is found
  - message # first string capture after the match will be mapped to the variable ${message}
  - arg? # this second argument is optional and will be available as ${arg}, ie. the rule will process even if this argument is missing.

hear: "bots are cool" # text match to trigger rule on if that phrase/regex pattern comes up in a room the bots been invited to. Hear currently takes no arguments.

schedule: "@every 10s" # cronspec for when you want a rule to be triggered on a schedule/interval. Schedule takes no arguments.
# example working cron specs:
# - 0 * * * * (at minute 0)
# - @every 1m
# - @every 10s

reaction: eyes # this will prompt the bot to add an emoji reaction to the message that triggered the rule. This emoji reaction can be modified later when actions are taken.

# actions
actions: # this rule takes no actions, just collects and responds with what is laid out in the format_output section

# response
format_output: "${message}" # slack specific message formatting

allow_users: # restrict usage to certain users
  - Jane.Doe
allow_usergroups: # restrict usage to certain usergroups

allow_user_ids: # restrict usage to certain user ids
 - F123456

ignore_users: # users in this list won't trigger this rule
  - John.Doe
ignore_usergroups: # users that are part of any of these groups won't trigger this rule
  - admin-team

start_message_thread: true # start a new thread on the message that triggered the rule

ignore_threads: true # will prevent the rule from triggering inside threaded messages

direct_message_only: false # will only reply via direct message if enabled

limit_to_rooms: # this is an array of rooms/channels where the rule can get triggered.
  - coolchannel

output_to_rooms: # this is an array of rooms/channels you want messages to be sent into in Slack.
  - mychannel

output_to_users: # this is an array of users you want messages to be sent to directly in Slack.
  - John.Doe

# help
help_text: echo <message> # help/usage text for the echo rule
include_in_help: true # see help_text in help message
```

* **name** (_string_) Choose a name of the rule. This should match the name of yaml file, should be short and descriptive.
* **active** (_boolean_) If set to true, the bot will look for the match in the rooms it is in and take stated actions.
* **respond** (_string_) Choose a string to match or regex pattern that this rule should trigger actions for.
  * **args** (_yaml array_) The number of items in this array determines how many args you're trying to capture after the match is found. Each argument captured after the match will be mapped to a variable name you assign in this array. Optional arguments (denoted with a `?` suffix) have to be listed last - their variable name will not contain the `?`.
* **hear** (_string_) Choose a string to match or regex pattern that this rule should trigger actions for. Hear will match any occurrence of the pattern in the room regardless of whether the bot is addressed. This trigger takes no arguments.
* **schedule** (_string_) [Cronspec](https://godoc.org/github.com/robfig/cron#hdr-CRON_Expression_Format) for when you want a rule to run on a schedule.
* **reaction** (_string_) Any time a rule is matched by the bot, it will add this reaction to the message that triggered the rule.
  * Slack: A string value of an [emoji](https://www.webpagefx.com/tools/emoji-cheat-sheet/) reaction that exists in the server. For example, putting a value of `white_check_mark` would add the reaction of ✅.
  * Discord: A string value of an [emoji](https://www.webpagefx.com/tools/emoji-cheat-sheet/) reaction that exists in the server. For example, putting a value of ✅ would add the reaction of ✅.
* **actions** (_yaml array_) Each item of this array determines sequential actions to take when a match is found. This can range from making an API call to running a script and grabbing the output. An empty actions section will just reply with a message you specify. See the [Actions](#action-examples) section below, rule examples for how the framework supports basic bot functions like HTTP requests and script execution. Actions will by default timeout after 10 seconds. If a longer timeout is needed, it is configurable to do so.
* **format_output** (_string_) This is the response shown to the user when a match is found, is often passed information from the actions section.
* **allow_users** (_yaml array_) An array of users that are permitted to execute this rule.
* **allow_usergroups** (_yaml array_) An array of user groups that are permitted to execute this rule. **Important:** To use this feature in Slack, you need to set up your integration as an app (not a regular bot configuration).
* **ignore_users** (_yaml array_) An array of users that will not be able to execute this rule.
* **ignore_usergroups** (_yaml array_) An array of user groups that will not be able to execute this rule. **Important:** To use this feature in Slack, you need to set up your integration as an app (not a regular bot configuration).
* **start_message_thread** (_boolean_) If set to true, the bot start a thread with your trigger message being the parent. Otherwise, the bot will post a message in the same channel.
* **ignore_threads** (_boolean_) If set to true, the bot will not be able to get triggered for this rule from within a message a thread.
* **direct_message_only** (_boolean_) If set to true, the bot will never respond to this rule in a room it will only direct message the sender.
* **limit_to_rooms** (_yaml array_) An array of rooms where the rule can get triggered.
* **output_to_rooms** (_yaml array_) An array of rooms you want messages to be sent into.
* **output_to_users** (_yaml array_) An array of specific users you want messages to be to.
* **help_text** (_string_) This is the response we show to the user when they interact with the bot and no match occurs. We often like to put bot usage instructions for the rule here.
* **include_in_help** (_boolean_) If set to true, this will include the help text set above in the help message. Setting to false is good for when you're trying to hide some rules/functionality to end users (perhaps admin-only functions).
