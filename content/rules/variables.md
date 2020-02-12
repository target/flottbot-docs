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
${_channel.id} channel ID where the message originated from.
${_channel.name} channel name where the message originated from [^1]
${_is_thread_message} indicates that the rule was triggered from within a message thread, returns "true" or "false"
${_source.link} will provide you with a link to the original message [^2]
```

- [^1]: for Slack, this might be an empty string if it originated from a direct message
- [^2]: this feature is currently only available for Slack


In addition, the following user-specific variables - populated with details of the user that triggered a rule match via a message - are available:

```sh
${_user.email}
${_user.name}
${_user.firstname} [^1]
${_user.lastname} [^1]
${_user.id} 
${_user.team} [^1]
${_user.teamid} [^1]
${_user.color} [^1]
${_user.realname} [^1]
${_user.tz} [^1]
${_user.tzlabel} [^1]
${_user.tzoffset} [^1]
${_user.realnamenormalized} [^1]
${_user.displayname} [^1]
${_user.displaynamenormalized} [^1]
${_user.skype} [^1]
${_user.phone} [^1]
${_user.title} [^1]
${_user.statustext} [^1]
${_user.statusemoji} [^1]
```

- [^1]: Only available for Slack, currently. See https://api.slack.com/types/user for more information
