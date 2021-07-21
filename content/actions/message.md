+++
title = "message"
weight = 4
toc = true
+++

The following is an example of sending output to the `#general` channel.

```yaml
name: message-rule
active: true
respond: message
actions:
  - name: trigger message
    type: message
    message: "This is a message from your bot."
    output_to_rooms:
      - general
```
