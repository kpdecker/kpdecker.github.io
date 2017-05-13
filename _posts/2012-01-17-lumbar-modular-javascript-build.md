---
layout: post
title: 'Lumbar: Modular Javascript Build'
date: 2012-01-17 12:01:22.000000000 -06:00
categories:
- Web Dev
tags:
- "@WalmartLabs"
- HTML5
- Lumbar
- Mobile Development
- Performance
---
<p>Since joining @WalmartLab's mobile web team close to a year ago we have been very busy rebuilding the mobile web platform using Backbone, Handlebars, Stylus and other awesome open source tools and frameworks.</p>
<p>Even though these tools allow for some pretty impressive feats of javascript, we kept hitting inefficiencies both in execution and developer time due to resource loading and management.</p>
<p>
When dealing with handlebars for example, we consistently found that we were trying to fight against the system to manage the templates used by a view. We neither wanted to manually inline the template in the javascript as we all know that escaping a language within a language is just painful, ala Java running SQL, nor did we want to add the additional overhead of an additional request for deferred loading of templates or worse inlining all application templates within the html file.</p>
<p>
Being both constrained by the mobile environment and needing to operate at the scale of Walmart it became apparent very quickly that we needed to have some sort of build-time utility to manage this so the developer can focus on creating the application logic, throw it into the build tool and out comes a nicely optimized module of code and styles.</p>
<p>
<a href="http://walmartlabs.github.com/lumbar">Lumbar</a> along with the associated Lumbar projects such as <a href="https://github.com/walmartlabs/lumbar-loader">lumbar-loader</a> and <a href="https://github.com/walmartlabs/lumbar-long-expires">lumbar-long-expires</a> are the fruits of these efforts.</p>
<h2>Overview</h2>
<p>The Lumbar suite focuses on the management of all resources required to generate complex javascript applications across many different platforms and environments. It's primary goals are to easily package, optimize, link to resources such as javascript, html, templates, styles, images, and any other client-side resources that web application may require.</p>
<p>From it's initial implementation, Lumbar has focused on making client performance the number one goal, to this end it allows for chunking resources into distinct code modules, easy minification, inlining, and cache management.</p>
<h2>Configuration</h2>
<p>
Lumbar is configured through a simple JSON config file, defining modules which are simple, demand loaded, segments of the application.</p>
<p>A base module that loads the core application:<br />
[javascript]<br />
    &quot;base&quot;: {<br />
      &quot;scripts&quot;: [<br />
        {&quot;src&quot;: &quot;js/lib/zepto.js&quot;, &quot;global&quot;: true},<br />
        {&quot;src&quot;: &quot;js/lib/underscore.js&quot;, &quot;global&quot;: true},<br />
        {&quot;src&quot;: &quot;js/lib/backbone.js&quot;, &quot;global&quot;: true},<br />
        {&quot;src&quot;: &quot;js/lib/handlebars.js&quot;, &quot;global&quot;: true},<br />
        {&quot;src&quot;: &quot;js/lib/thorax.js&quot;, &quot;global&quot;: true},<br />
        {&quot;src&quot;: &quot;js/lib/script.js&quot;, &quot;global&quot;: true},<br />
        {&quot;src&quot;: &quot;js/lib/lumbar-loader.js&quot;, &quot;platform&quot;: &quot;web&quot;},<br />
        {&quot;src&quot;: &quot;js/lib/lumbar-loader-standard.js&quot;, &quot;platform&quot;: &quot;web&quot;},<br />
        {&quot;src&quot;: &quot;js/lib/lumbar-loader-backbone.js&quot;, &quot;platform&quot;: &quot;web&quot;},<br />
        &quot;js/init.js&quot;,<br />
        {&quot;src&quot;: &quot;js/bridge.js&quot;, &quot;platforms&quot;: [&quot;iphone&quot;, &quot;ipad&quot;, &quot;android&quot;]},<br />
        {&quot;src&quot;: &quot;js/bridge-android.js&quot;, &quot;platform&quot;: &quot;android&quot;},<br />
        {&quot;src&quot;: &quot;js/bridge-ios.js&quot;, &quot;platforms&quot;: [&quot;ipad&quot;,&quot;iphone&quot;]},<br />
        {&quot;module-map&quot;: true}<br />
      ],<br />
      &quot;styles&quot;: [<br />
        &quot;styles/base.styl&quot;,<br />
        {&quot;src&quot;: &quot;styles/iphone.styl&quot;, &quot;platform&quot;: &quot;iphone&quot;},<br />
        {&quot;src&quot;: &quot;styles/android.styl&quot;, &quot;platform&quot;: &quot;android&quot;},<br />
        {&quot;src&quot;: &quot;styles/ipad.styl&quot;, &quot;platform&quot;: &quot;ipad&quot;},<br />
        {&quot;src&quot;: &quot;styles/web.styl&quot;, &quot;platform&quot;: &quot;web&quot;}<br />
      ],<br />
      &quot;static&quot;: [<br />
        {&quot;src&quot;: &quot;static/#{platform}/index.html&quot;, &quot;dest&quot;: &quot;index.html&quot;}<br />
      ]<br />
    },<br />
[/javascript]<br /></p>
<p>A router-linked module. When using backbone/thorax integration, this module will automatically load when the # or #hello routes are navigated:<br />
[javascript]<br />
    &quot;hello-world&quot;: {<br />
      &quot;routes&quot;: {<br />
        &quot;&quot;: &quot;index&quot;,<br />
        &quot;hello&quot;: &quot;index&quot;<br />
      },<br />
      &quot;scripts&quot;: [<br />
        &quot;js/views/hello-world&quot;,<br />
        &quot;js/routers/hello-world.js&quot;<br />
      ],<br />
      &quot;styles&quot;: [<br />
        &quot;styles/hello-world.styl&quot;<br />
      ]<br />
    }<br />
[/javascript]</p>
<p>
This config file also defines the platforms that lumbar will generate, allowing for customizing the modules for different environments (<code>"platforms": [ "android", "iphone", "ipad", "web" ]</code>). At @WalmartLabs we utilize this to serve customized experiences for the native clients utilizing the same codebase.</p>
<div class="aligncenter">
  <a href="http://static.incaseofstairs.com/uploads/2012/01/walmart-checkout-devices.png"><br />
    <img src="{{ site.baseurl }}/assets/walmart-checkout-devices.png" alt="Walmart Checkout Designs" title="Walmart Checkout Designs" width="1024" height="487" class="size-full wp-image-493" /><br />
  </a></p>
<p class="wp-caption wp-caption-text">Single stylus codebase styled for iPhone, Android, and mobile web platforms.</p>
</div>
<div class="aligncenter">
  <a href="http://static.incaseofstairs.com/uploads/2012/01/walmart-ipad-checkout.png"><br />
    <img src="{{ site.baseurl }}/assets/walmart-ipad-checkout.png" alt="Walmart Checkout - iPad" title="Walmart Checkout - iPad" width="620" height="513" class="size-full wp-image-494" /><br />
  </a></p>
<p class="wp-caption wp-caption-text">More dramatic customizations of the same codebase utilizing platform conditional code.</p>
</div>
<p>
The final component of the configuration is the packages component, which allows for bundling specific modules into singular responses when a particular use case only uses a subset of modules, such as the native checkout implementations utilized by @WalmartLabs.</p>
<p>
[javascript]<br />
  &quot;packages&quot;: {<br />
    &quot;web&quot;: {<br />
      &quot;platforms&quot;: [ &quot;web&quot; ],<br />
      &quot;combine&quot;: false<br />
    },<br />
    &quot;native-hello-world&quot;: {<br />
      &quot;platforms&quot;: [ &quot;android&quot;, &quot;iphone&quot;, &quot;ipad&quot; ],<br />
      &quot;modules&quot;: [ &quot;base&quot;, &quot;hello-world&quot; ],<br />
      &quot;combine&quot;: true<br />
    }<br />
  },<br />
[/javascript]<br />
  Package declarations defining normal loading for web and combined for native platforms.</p>
<p>
On building this will generate the following structure:</p>
<pre>
    $ find . -type f
    ./android/index.html
    ./android/native-hello-world.css
    ./android/native-hello-world.js
    ./android/native-hello-world@1.5x.css
    ./ipad/index.html
    ./ipad/native-hello-world.css
    ./ipad/native-hello-world.js
    ./iphone/index.html
    ./iphone/native-hello-world.css
    ./iphone/native-hello-world.js
    ./iphone/native-hello-world@2x.css
    ./web/base.css
    ./web/base.js
    ./web/base@2x.css
    ./web/hello-world.css
    ./web/hello-world.js
    ./web/hello-world@2x.css
    ./web/index.html
</pre></p>
<p>
Which can be deployed as static resources to any web server, served via a CDN, or distributed by many other means.</p>
<p>
While not a core feature, the <a href="”https://github.com/walmartlabs/lumbar-long-expires”">lumbar-long-expires</a> plugin takes this a step further by allowing resources to include expires tokens in their names automatically. When enabled the above may generate content like the following, allowing for the application resources to be served with extended Expires headers.</p>
<pre>
    $ find . -type f
    ./android/cb188f8/native-hello-world.css
    ./android/cb188f8/native-hello-world.js
    ./android/cb188f8/native-hello-world@1.5x.css
    ./android/index.html
    ./ipad/cb188f8/native-hello-world.css
    ./ipad/cb188f8/native-hello-world.js
    ./ipad/index.html
    ./iphone/cb188f8/native-hello-world.css
    ./iphone/cb188f8/native-hello-world.js
    ./iphone/cb188f8/native-hello-world@2x.css
    ./iphone/index.html
    ./web/cb188f8/base.css
    ./web/cb188f8/base.js
    ./web/cb188f8/base@2x.css
    ./web/cb188f8/hello-world.css
    ./web/cb188f8/hello-world.js
    ./web/cb188f8/hello-world@2x.css
    ./web/index.html
</pre></p>
<p>
The @WalmartLabs mobile team believes strongly in open source software which is why in addition to open sourcing Lumbar we are also open sourcing our <a href="http://walmartlabs.github.com/thorax">Thorax</a> framework. If you are a backbone user looking to ease common tasks such as data binding and linking<br />
data operations to a particular route I urge you to take a look. It even integrates with Lumbar!</p>
