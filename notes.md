# Introduction

  Hello, I'm Matt Shwery.

  I'm a Frontend Developer from New Orleans. I work at this company, Federated Sample – a software and services company in the Market Research industry. We don't do Market Research ourselves, but build tools to faciliate it.

  Last year, our CEO Patrick Comer set a goal before our entire team: hit 10 Million completes on our platform. These are people who come into our platform to take a survey/poll/whatever and successfully finish it.

  In tandem, I was tasked to design and implement an internal tool to track our progress around that goal in real-time. While I could have turned to HighCharts or Flot or Chart.js or the many other tools out there to achieve very similar results, I settled on D3.js as a learning challenge and as an opportunity to explore a vast open landscape.

  I suppose it's ironic that I'm talking about boxing up some of that vast open landscape into a format that's a little more DRY, but I'm able to focus on our internal requirements by rolling my own.


# Why use D3.js if it's this much work for a simple standard chart?

  - don't use it if there's a library that supports your needs already, no need to reinvent the wheel

  BUT... if

  - you require a level of customization that most charting/plotting tools don't provide
  - you want greater versatility
  - you want to learn an indespensible skill, with a deep and varied toolset
  - you have some unique visualizations mixed with some simple ones (one tool instead of many)
  - you aim to support IE9+

  then by all means, you should consider d3.js

In my case, I had a few visuals that required deeper customization (styling, generated markup, labels and positioning) than what was possible with other libraries. I also had some very plain visuals, but didn't want to add more libraries if I could avoid it.

# Not all roses

  It was in my attempt to use D3 for the simple stuff that I realized what it was missing: opinions on organizing and reusing the large amount of boilerplate code that comes with each visualization.

# Existing tools built on top of d3

  There are some libraries that already exist that do this for you:

  - C3
  - reD3
  - NVD3
  - d3.chart

  They all have overlapping ideologies, but their implementations vary.

# Roll your own

  You can also roll your own. We're going to look at how we might go about doing that.

# Defining a baseline

  There are few things we want to keep in mind as we try to extend d3.js, adding our own opinions on structure and reusability to the mix:

  1. Extensible - D3 is an extension of standard HTML, CSS, so we want to continue in that spirit of extensibility
  2. Configurable - we need to add a layer of configuration if we are going to abstract away some of the boilerplate
  3. Declarative - we want to keep with D3's declarative style for consistency and in some cases transparency
  4. Repeatable - and most obviously we want to be able to reuse charts

# Setup

  There are many ways you can setup instantiation of charts, but d3's creator Mike Bostock sums it up nicely:

  > implement charts as closures with getter-setter methods

  This is at the core of all the tools trying to provide reusable charts on top of d3.js

  ```
  var r3 = (function() {

    var defaults = {};

    var charts = {
      'bar': function(config) { /* bar chart specific generation here */ },
      'line': function(config) { /* line chart specific generation here */ },
      'pie': function(config) { /* pie chart specific generation here */ }
    };

    function chartGenerator(config) {
      // enforce a valid chart-type
      if (!charts[config.type]) {
        throw new Error("Must specify a valid chart type in your configuration parameters");
      } else {
        // actually call the proper generator
        return charts[config.type].apply(this, config);
      }
    }

    return chartGenerator;

  })();
  ```

# Repeatable

  One aspect of this that isn't immediately obvious is that you might want to be able to easily detach the blueprints for a chart from one node and attach them to another.
