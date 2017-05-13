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
Google has the <a href="http://www.polymer-project.org/">Polymer</a> framework which polyfils web components. The premise is that you can create truly isolated components that are drop in and do not have worries such as CSS impact from other components.

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
Facebook is doing similar work with their <a href="http://facebook.github.io/react/">React</a> framework. They have created a new compiled to JavaScript language that allows one to embed html into the source file:

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
<a href="http://twitter.github.io/flight/">Flight</a> from Twitter attempts to handle componetization at a much smaller level. They focus on defining interfaces between discrete components. Each of these components can focus on only their implementation.

This is lower-level than the systems above and in many ways can be used to build other framework
 systems.

### Performance
Performance was a huge concern at all 3 conferences with many sessions devoted to different aspects of performance improvements. My major take aways are:

<ul>
<li>
Don't follow a coding pattern because something is "faster".<br />
What exactly is faster will change as runtimes optimize for the code that they are seeing. Instead look at what the tools are saying and react accordingly.

</li>
<li>
Don't prematurely optimize.<br />
You are probably wrong. Your code is most likely going to be harder to understand and there is plenty that you can spend time as engineering time is finite.

</li>
<li>
Performance is holistic.

<ul>
<li>JavasScript, layout, rendering, compositing, GC, etc. can all impact performance.</li>
<li>Newer architectures are moving behaviors out to other threads but this does not work if you try to implement your own behavior. Custom scrolling is the biggest offender here.</li>
<li>Under current Chrome implementations touch handlers can cause scrolling issues as it disables certain scrolling optimizations.</li>
</ul>
</li>
<li>
Chrome is the leader in debugging tools and ability to investigate performance

<ul>
<li>Chrome dev tools timeline panel</li>
<li>about:tracing - Google's internal performance tool</li>
<li>V8 tools - In depth V8 profiling tools</li>
<li>Continuous painting mode lets you test performance impact of specific elements or properties</li>
<li>Remote debugging API (This powers Chrome for Android debugging and topcoat performance monitor)</li>
</ul>
</li>
<li>
Higher level <code>console</code> commands are useful

<ul>
<li><code>console.time</code></li>
<li><code>console.group</code></li>
<li>These are not always evil and can help programmers with onboarding</li>
</ul>
</li>
<li>
Topcoat has a turbo mode which attempts to remove layout and style calculation from overhead.<br />
All elements have style element and are absolutely positioned. On a personal note this seems a bit overboard for 99% of use cases.

</li>
<li>
High-speed cameras can be used in the name of performance testing! :)

</li>
</ul>
Many V8 specific performance recommendations as well:

<ul>
<li>
V8 has an optimized/unoptimized state machine that a particular piece of code may jump between.<br />
Can't tell at compile time if something will be promoted to optimized once hot. When profiling should look for <code>*</code> token next to function names to verify that it's optimized.

</li>
<li>
Memory allocation pattern between optimized and unoptimized are dramatically different. Optimized will often remove temp variables wereas unoptimized will have to GC temp variables.

</li>
<li>
Can monitor state machine with <code>--js-flags="--trace-deopt --trace-opt-verbose"</code>. WARN: Very verbose. Pipe &amp; Grep

</li>
</ul>

### Better Tooling
Adobe and Google had strong developer tools showings at these conferences.

Adobe demoed some very cool features for their brackets IDE including:

<ul>
<li><a href="http://www.youtube.com/watch?v=xAP8CSMEwZ8&amp;feature=player_embedded">PSDLens</a> - PSD inspector assistant</li>
<li><a href="http://www.youtube.com/watch?feature=player_embedded&amp;v=kXTP8XqrSwE">Response</a> - Responsive design editor</li>
<li><a href="https://github.com/adobe-research/theseus">Theseus</a> - Brackets debugger concept</li>
<li>Instabug - Live code inspector with visual unit test executor/generator</li>
</ul>
Additionally they covered some of the tools involved with their topcoat project:

<ul>
<li><a href="http://bench.topcoat.io">Continuous Performance server</a></li>
<li><a href="https://github.com/topcoat/topcoat/blob/master/package.json#L6-27">Conditional componentized build</a></li>
</ul>
On the Google front, most of the improvements were in the Chrome Developer Tools including:

<ul>
<li>Flame charts</li>
<li>Full remote dev tools on Chrome For Android</li>
<li>SASS support, generically via source maps</li>
<li>Live editing with mapped update support</li>
</ul>

### JavasScript everywhere
One of the other fun themes was the use of JavaScript in more and more places. This included a hacking day on various bots and AR Drones for those who choose those sessions. This culminated with a Nodebot <a href="http://www.youtube.com/watch?v=KgdyrvWOkUo">shooting down a NodeCopter</a> and calls for <a href="https://twitter.com/horse_js/status/340578333702115329">Node-based Aircraft Carriers</a>.

## Random Coolness

<ul>
<li><a href="http://jankfree.org">jankfree.org</a></li>
<li><a href="http://nodesecurity.io">nodesecurity.io</a></li>
<li><a href="http://webplatform.org">webplatform.org</a></li>
<li>@rem demoing game with RTC and getUserMedia</li>
<li>@seb_ly giving an amazing presentation including crowd participation from 5000 miles away and live coding over VNC</li>
<li>@necolas - Get over yourself. Tech changes every few years and all of the battles today will likely be moot in a few years.</li>
</ul>
Outside of the above there we're a lot of fun and interesting sessions that could be discussed in depth but suffice to say that the JavasScript and Web Development communities are very strong and there is a lot of interesting stuff coming down the pipes.

