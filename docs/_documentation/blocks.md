---
layout: documentation
description: "Blocks are the foundation of workflows. Each one represents a step to be completed."
icon: "fa-th-large"
title:  "Blocks"
---

* TOC
{:toc}

## Blocks

Blocks are the foundation of workflows. Each one represents a step to be completed. Along the way, each block passes a value from one block to the next, if they fork each block will get their own copy of the current value.

All blocks can have the following properties:

* `id` *int*: Unique identifier of the block, used to help make connections.
* `connections` *int[]*: Blocks to execute if one of the results are chosen.
* `type` *string*: The name of the block you wish to use.

Each unique block type can have it's own properties, listed below with their
descriptions.

## Input Blocks

Input blocks are blocks that are the entry points to your plugin. These usually
return results to be displayed in Zazu.

{% highlight javascript %}
module.exports = {
  blocks: {
    input: [
      // input blocks
    ]
  }
};
{% endhighlight %}

### Root Script

This allows you to execute your script without any input prefixes.

Variables defined in the [configuration](/documentation/configuration/) will be used as
environment variables in the script call.

* `respondsTo` *function*: Filter input so your plugin doesn't run after each keystroke
* `script` *string*: Shell command that returns results

{% highlight javascript %}
{
  id: 1,
  type: 'RootScript',
  respondsTo: (input) => {
    const hasEquation = input.match(/^[\d\.\(\)\+\-*\/\s]+$/)
    const hasNumbers = input.match(/\d/)
    return hasEquation && hasNumbers
  },
  script: 'node calculator.js {query}',
  connections: [2],
}
{% endhighlight %}

### Prefix Script

This allows you to execute your script with a prefix.

Variables defined in the [configuration](/documentation/configuration/) will be used as
environment variables in the script call.

* `prefix` *string*: Prefix to be used before user input. 
* `space` *boolean*: If a space should be between the Prefix and the user input.
* `args` *string*: Specifies if you want arguments. Possibles values are `Required`, `Optional` and `None`.
* `script` *string*: Shell command that returns results

{% highlight javascript %}
{
  id: 1,
  type: 'PrefixScript',
  prefix: 'calc',
  space: true,
  args: 'Required',
  script: 'node calculator.js {query}',
  connections: [2],
}
{% endhighlight %}

## Output Blocks

Output blocks happen after an input block, these can do several things like call
a script, or copy something to your clipboard.

Most of the special attributes allow you to use a variable `{value}` which will
be replaced with the current value of the result being passed around.

{% highlight javascript %}
module.exports = {
  blocks: {
    output: [
      // output blocks
    ]
  }
};
{% endhighlight %}

### Copy To Clipboard

This block will copy the given input to the clipboard.

* `text` *string*: Text to be copied to the clipboard.

{% highlight javascript %}
{
  id: 2,
  type: 'CopyToClipboard',
  text: '{value}',
}
{% endhighlight %}

### Open In Browser

Open up the value in the users default Browser.

* `url` *string*: URL of the page you wish to open.

{% highlight javascript %}
{
  id: 3,
  type: 'OpenInBrowser',
  url: '{value}',
}
{% endhighlight %}

### Send Notification

Give the user a notification with a title and a message.

* `title` *string*: Title of the notification.
* `message` *string*: Message of the notification.

{% highlight javascript %}
{
  id: 4,
  type: 'SendNotification',
  title: 'Hello world',
  message: '{value}',
}
{% endhighlight %}

### User Script

For those more unique actions, you can run any script you need.

Variables will be passed as environment variables, but this plugin cannot mutate
the state.

* `script` *string*: The shell command to run to return the results.

{% highlight javascript %}
{
  id: 5,
  type: 'UserScript',
  script: 'ruby output.rb {value}',
}
{% endhighlight %}