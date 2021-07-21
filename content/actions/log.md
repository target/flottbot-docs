+++
title = "log"
weight = 3
toc = true
+++

The following is an example of logging output to the `#bot-log` channel when anyone says the word *breakfast* in any room the bot has joined.

```yaml
name: breakfast-monitoring
active: true
hear: breakfast
actions:
  - name: trigger log
    type: log
    message: "${_user.firstname} ${_user.lastname} is talking about breakfast."
    output_to_rooms:
      - bot-log
```
