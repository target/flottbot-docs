+++
title = "Rules"
weight = 5
sort_by = "weight"
+++

A rule is where you will define what you want your bot to respond to and what actions you want your bot to take when a rule is triggered. Rules are yaml files located in the config/rules/ directory where each yaml file will be treated as a separate rule. Any string in your yaml file wrapped in `${}` will either be parsed and interpreted to use an environment variable (set where the bot is run) OR be mapped/stored as a result of an action within the yaml file to be accessed later during the bot's execution.
