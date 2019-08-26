+++
title = "Variables"
weight = 2
+++

The below variables are set by default:

```sh
${_raw_http_output} raw output from an HTTP request action.
${_raw_http_status} response code from an HTTP request action.
${_exec_output} stdout output from an exec action.
${_exec_status} the status code from an exec action.
${_raw_user_input} raw message from a user captured by the bot when a match is found.
${_user.email} email of user that sent a message that triggered a match.
${_user.name} name of user that sent a message that triggered a match.
${_user.firstname} first name of user that sent a message that triggered a match.
${_user.lastname} last name of user that sent a message that triggered a match.
${_user.id} user ID of user that sent a message that triggered a match.
${_channel.id} channel ID where the message originated from.
${_channel.name} channel name where the message originated from[1]
${_is_thread_message} indicates that the rule was triggered from within a message thread, returns "true" or "false"
```

_1. for Slack, this might be an empty string if it originated from a direct message_

**Note:** `${_user.firstname}` and `${_user.lastname}` are not populated for Discord, you should instead utilize `${_user.name}`.
