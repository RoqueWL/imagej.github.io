---
mediawiki: Script_Interpreter
title: Script Interpreter
---


{% capture author%}
{% include person id='dscho' %}, {% include person id='ctrueden' %}
{% endcapture %}

{% capture maintainer%}
{% include person id='ctrueden' %}
{% endcapture %}

{% capture source%}
{% include github org='scijava' repo='script-editor' branch='master' source='org/scijava/ui/swing/script' %}
{% endcapture %}
{% include info-box name='Script Interpreter' software='ImageJ' author=author maintainer=maintainer source=source released='8 Apr 2016' status='active' %}
 The **Script Interpreter** is a {% include wikipedia title="Read–eval–print loop" %} allowing scripting from the command line.

## Getting Started

Start it via {% include bc path='Plugins|Scripting|Script Interpreter'%} or by typing {% include key key='Shift|[' %}.

![Script_Interpreter.png](/media/scripting/script-interpreter.png)

## Usage

### Get the active dataset

### Run an op

## Issues

The Script Interpreter is under active development. Currently, [Groovy](/scripting/groovy) works fine, but there some issues with other scripting languages.
