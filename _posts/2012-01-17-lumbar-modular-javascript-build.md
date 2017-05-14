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
excerpt_separator: <!--more-->
---

Since joining @WalmartLab's mobile web team close to a year ago we have been very busy rebuilding the mobile web platform using Backbone, Handlebars, Stylus and other awesome open source tools and frameworks

Even though these tools allow for some pretty impressive feats of javascript, we kept hitting inefficiencies both in execution and developer time due to resource loading and management.

When dealing with handlebars for example, we consistently found that we were trying to fight against the system to manage the templates used by a view. We neither wanted to manually inline the template in the javascript as we all know that escaping a language within a language is just painful, ala Java running SQL, nor did we want to add the additional overhead of an additional request for deferred loading of templates or worse inlining all application templates within the html file.

Being both constrained by the mobile environment and needing to operate at the scale of Walmart it became apparent very quickly that we needed to have some sort of build-time utility to manage this so the developer can focus on creating the application logic, throw it into the build tool and out comes a nicely optimized module of code and styles.

[Lumbar](http://walmartlabs.github.com/lumbar) along with the associated Lumbar projects such as [lumbar-loader](https://github.com/walmartlabs/lumbar-loader) and [lumbar-long-expires](https://github.com/walmartlabs/lumbar-long-expires) are the fruits of these efforts.

<!--more-->


## Overview

The Lumbar suite focuses on the management of all resources required to generate complex javascript applications across many different platforms and environments. It's primary goals are to easily package, optimize, link to resources such as javascript, html, templates, styles, images, and any other client-side resources that web application may require.

From it's initial implementation, Lumbar has focused on making client performance the number one goal, to this end it allows for chunking resources into distinct code modules, easy minification, inlining, and cache management.

## Configuration

Lumbar is configured through a simple JSON config file, defining modules which are simple, demand loaded, segments of the application.

A base module that loads the core application:

```javascript
{
  "base": {
    "scripts": [
      {"src": "js/lib/zepto.js", "global": true},
      {"src": "js/lib/underscore.js", "global": true},
      {"src": "js/lib/backbone.js", "global": true},
      {"src": "js/lib/handlebars.js", "global": true},
      {"src": "js/lib/thorax.js", "global": true},
      {"src": "js/lib/script.js", "global": true},
      {"src": "js/lib/lumbar-loader.js", "platform": "web"},
      {"src": "js/lib/lumbar-loader-standard.js", "platform": "web"},
      {"src": "js/lib/lumbar-loader-backbone.js", "platform": "web"},
      "js/init.js",
      {"src": "js/bridge.js", "platforms": ["iphone", "ipad", "android"]},
      {"src": "js/bridge-android.js", "platform": "android"},
      {"src": "js/bridge-ios.js", "platforms": ["ipad","iphone"]},
      {"module-map": true}
    ],
    "styles": [
      "styles/base.styl",
      {"src": "styles/iphone.styl", "platform": "iphone"},
      {"src": "styles/android.styl", "platform": "android"},
      {"src": "styles/ipad.styl", "platform": "ipad"},
      {"src": "styles/web.styl", "platform": "web"}
    ],
    "static": [
      {"src": "static/#{platform}/index.html", "dest": "index.html"}
    ]
  }
}
```

A router-linked module. When using backbone/thorax integration, this module will automatically load when the # or #hello routes are navigated:

```json
{
  "hello-world": {
    "routes": {
      "": "index",
      "hello": "index"
    },
    "scripts": [
      "js/views/hello-world",
      "js/routers/hello-world.js"
    ],
    "styles": [
      "styles/hello-world.styl"
    ]
  }
}
```

This config file also defines the platforms that lumbar will generate, allowing for customizing the modules for different environments (`"platforms": [ "android", "iphone", "ipad", "web" ]`). At @WalmartLabs we utilize this to serve customized experiences for the native clients utilizing the same codebase.

{% include image.html
    src='assets/walmart-checkout-devices.png'
    alt='Walmart Checkout Designs'
    caption='Single stylus codebase styled for iPhone, Android, and mobile web platforms.'
%}
{% include image.html
    src='assets/walmart-ipad-checkout.png'
    alt='Walmart Checkout - iPad'
    caption='More dramatic customizations of the same codebase utilizing platform conditional code.'
%}

The final component of the configuration is the packages component, which allows for bundling specific modules into singular responses when a particular use case only uses a subset of modules, such as the native checkout implementations utilized by @WalmartLabs.


```json
"packages": {
  "web": {
    "platforms": [ "web" ],
    "combine": false
  },
  "native-hello-world": {
    "platforms": [ "android", "iphone", "ipad" ],
    "modules": [ "base", "hello-world" ],
    "combine": true
  }
},
```

Package declarations defining normal loading for web and combined for native platforms.


On building this will generate the following structure:

```
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
```

Which can be deployed as static resources to any web server, served via a CDN, or distributed by many other means.

While not a core feature, the [lumbar-long-expires](”https://github.com/walmartlabs/lumbar-long-expires”) plugin takes this a step further by allowing resources to include expires tokens in their names automatically. When enabled the above may generate content like the following, allowing for the application resources to be served with extended Expires headers.

```
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
```

The @WalmartLabs mobile team believes strongly in open source software which is why in addition to open sourcing Lumbar we are also open sourcing our [Thorax](http://walmartlabs.github.com/thorax) framework. If you are a backbone user looking to ease common tasks such as data binding and linking<br />
data operations to a particular route I urge you to take a look. It even integrates with Lumbar!
