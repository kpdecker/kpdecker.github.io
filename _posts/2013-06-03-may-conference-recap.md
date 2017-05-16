---
layout: post
title: May Conference Recap
categories:
- Web Dev
tags:
- Performance
- Web Development
---

I had the pleasure of attending portions of Google IO, CSS Conf, and JS Conf and wanted to summarize my notes on these conferences. These are my own take aways and may not have been what the presenter intended. If I'm completely off base, please feel free to drop me a line. This is also not inclusive as there are only so many sessions that one can attend :)

## Major Trends

### Componentization

Biggest theme of all of these conferences was attempting to create ways of compoentizing our code so that we can leave the smell of the poo all in one place, to paraphrase @danwrong.

#### Polymer
Google has the [Polymer](http://www.polymer-project.org/) framework which polyfils web components. The premise is that you can create truly isolated components that are drop in and do not have worries such as CSS impact from other components.

```html
<element name="my-element">
  <section>
    I'm a my-element!
  </section>
  <footer>nothing to see here</footer>
  <script>
    // When <element> is in document, might run in wrong context.
    // Only do work when this == <element>.
    if (this !== window) {
      var section = this.querySelector('section');
      this.register({
        prototype: {
          readyCallback: function() {
            this.innerHTML = section.innerHTML;
          }
        }
     });
    }
  </script>
</element>
```

This looks pretty amazing and I think will provide some awesome separation of concerns between content and behaviors in the future. Polyfilling this behavior seems outright genius but at the same time scares me as you are basing a lot of your implementation on something that is being hacked into the browser.

I'm of the opinion that this is amazing future looking work but is a bit more risk than I'd like to take on for my user base.

With standards support for this moving forward I think this is where we will be in 5-10 years depending on the user base that a project is required to support. I also believe that there is a huge opportunity here for developer tooling as this changes some things up that will require changes and improvements to our existing hint/test/minimize/etc tool stacks.


#### React
Facebook is doing similar work with their [React](http://facebook.github.io/react/) framework. They have created a new compiled to JavaScript language that allows one to embed html into the source file:

```javascript
var HelloMessage = React.createClass({
  render: function() {
    return <div>{'Hello ' + this.props.name}</div>;
  }
});
```

The framework will then manage the all of the rendering lifecycle without data binding or blanket re-rendering. This is apparently done by a DOM diffing algorithm that walks the DOM tree and updates only the components that need to be on a property change. The presenting team highlighted that you can provide hints to this implementation to improve performance so I do question how well this scales.

I have not had a chance to play around with this implementation but I am not against the html embedded in a JavaScript file like many people in the blogosphere are/have been. I think both this and polymer tackle separation of concerns in a different manner than the HTML/CSS/JS paradigm that has been beat into web developers for so long. I see them as implementing separation of concerns at a content vs. behavior level which may be better for some workflows.


#### Flight
[Flight](http://twitter.github.io/flight/) from Twitter attempts to handle componetization at a much smaller level. They focus on defining interfaces between discrete components. Each of these components can focus on only their implementation.

This is lower-level than the systems above and in many ways can be used to build other framework
 systems.

### Performance
Performance was a huge concern at all 3 conferences with many sessions devoted to different aspects of performance improvements. My major take aways are:

- Don't follow a coding pattern because something is "faster".

  What exactly is faster will change as runtimes optimize for the code that they are seeing. Instead look at what the tools are saying and react accordingly.

- Don't prematurely optimize.

  You are probably wrong. Your code is most likely going to be harder to understand and there is plenty that you can spend time as engineering time is finite.

- Performance is holistic.

  - JavasScript, layout, rendering, compositing, GC, etc. can all impact performance.
  - Newer architectures are moving behaviors out to other threads but this does not work if you try to implement your own behavior. Custom scrolling is the biggest offender here.
  - Under current Chrome implementations touch handlers can cause scrolling issues as it disables certain scrolling optimizations.

- Chrome is the leader in debugging tools and ability to investigate performance

  - Chrome dev tools timeline panel
  - about:tracing - Google's internal performance tool
  - V8 tools - In depth V8 profiling tools
  - Continuous painting mode lets you test performance impact of specific elements or properties
  - Remote debugging API (This powers Chrome for Android debugging and topcoat performance monitor)

- Higher level `console` commands are useful

  - `console.time`
  - `console.group`
  - These are not always evil and can help programmers with onboarding

- Topcoat has a turbo mode which attempts to remove layout and style calculation from overhead.

  All elements have style element and are absolutely positioned. On a personal note this seems a bit overboard for 99% of use cases.

- High-speed cameras can be used in the name of performance testing! :)


Many V8 specific performance recommendations as well:

- V8 has an optimized/unoptimized state machine that a particular piece of code may jump between.

  Can't tell at compile time if something will be promoted to optimized once hot. When profiling should look for `*` token next to function names to verify that it's optimized.

- Memory allocation pattern between optimized and unoptimized are dramatically different. Optimized will often remove temp variables wereas unoptimized will have to GC temp variables.

- Can monitor state machine with `--js-flags="--trace-deopt --trace-opt-verbose"`. WARN: Very verbose. Pipe & Grep

### Better Tooling
Adobe and Google had strong developer tools showings at these conferences.

Adobe demoed some very cool features for their brackets IDE including:

- [PSDLens](http://www.youtube.com/watch?v=xAP8CSMEwZ8&amp;feature=player_embedded) - PSD inspector assistant
- [Response](http://www.youtube.com/watch?feature=player_embedded&amp;v=kXTP8XqrSwE) - Responsive design editor
- [Theseus](https://github.com/adobe-research/theseus) - Brackets debugger concept
- Instabug - Live code inspector with visual unit test executor/generator

Additionally they covered some of the tools involved with their topcoat project:


- [Continuous Performance server](http://bench.topcoat.io)
- [Conditional componentized build](https://github.com/topcoat/topcoat/blob/master/package.json#L6-27)

On the Google front, most of the improvements were in the Chrome Developer Tools including:

- Flame charts
- Full remote dev tools on Chrome For Android
- SASS support, generically via source maps
- Live editing with mapped update support

### JavasScript everywhere
One of the other fun themes was the use of JavaScript in more and more places. This included a hacking day on various bots and AR Drones for those who choose those sessions. This culminated with a Nodebot [shooting down a NodeCopter](http://www.youtube.com/watch?v=KgdyrvWOkUo) and calls for [Node-based Aircraft Carriers](https://twitter.com/horse_js/status/340578333702115329).

## Random Coolness

- [jankfree.org](http://jankfree.org)
- [nodesecurity.io](http://nodesecurity.io)
- [webplatform.org](http://webplatform.org)
- @rem demoing game with RTC and getUserMedia
- @seb_ly giving an amazing presentation including crowd participation froaway and live coding over VNC
- @necolas - Get over yourself. Tech changes every few years and all otoday will likely be moot in a few years.

Outside of the above there we're a lot of fun and interesting sessions that could be discussed in depth but suffice to say that the JavasScript and Web Development communities are very strong and there is a lot of interesting stuff coming down the pipes.

