---
layout: post
title:  "How to Love Sprockets and Grunt/Gulp at the Same Time"
date:   2015-07-27 11:23:23
author: Philip Thrasher
categories: javascript sprockets build
---

## Objective

First, a bit of backstory. We have an existing core site built with Rails using the traditional asset pipeline. Our first objective was implementing two new mobile only single page web apps backed by our Rails application. We had a secondary objective to take advantage of all the great tools the javascript community has to offer, for example; existing modules, NPM, good debugging tools, live reloading, linting, fast dev-time compilation, testing frameworks, etc.

## What other people were doing

One possible approach is to [stop using Sprockets][1]. Standard functionality would be lost. Gone would be the [asset URL helpers][2] which makes using different CDNs for each environment and creating cache-busting asset digests so simple. This would require us to write code to search and replace URLs in our `.erbs` during deploy. Also, using gems with `.js` dependencies would no longer be as simple as adding a require line, making our setup non-standard.

[1]: http://blog.smart.ly/2014/01/21/goodbye-sprockets-a-grunt-based-rails-asset-pipeline/
[2]: http://api.rubyonrails.org/classes/ActionView/Helpers/AssetUrlHelper.html

A second [approach][3] is to use browserify-rails and mix and match require directives from Sprockets with require calls in your javascript assets.

[3]: http://blog.arkency.com/2015/04/bring-commonjs-to-your-asset-pipeline/

Neither of these choices meet our goal of low impact to our existing system and getting the js communities best tools at the same time.

## Our Solution

We build our new mobile javascript apps using [webpack][4] to create bundles.  Grunt runs all of our tasks (including webpack) and also runs a custom script that calculates the md5's of all static assets in the rails app. We use [Babel][5] and [SASS][6] plugins to replace asset URLs in our new `.js` and `.css`.

Since we have to be able to generate md5 digests that match Sprocket's md5'd filenames, we needed to be certain we calculated the md5's in the same way that Sprockets did. Sprocket's default was a bit more complicated than our needs required, so we altered the algorithm slightly.

**in confg/initializers/assets.rb**

```ruby
Rails.application.config.assets._blocks << proc { |assets|
  assets.version = Rails.env.to_s
}
```

This forces sprockets to use only the following for input:

`<sprockets version>``<rails environment>``<file contents>`

Prior to the above change, it used a number of different bits of meta information in addition to the file contents. For our needs, the above 3 pieces of info are sufficient. Our legacy javascript is handled by Sprockets in the usual way producing `application.js` and `application.css` with cache busting digests and `asset_url` tags reference the correct environment based CDN. Furthermore, since we are able to match the md5 digest, we don't lose any sprockets helpers in our rails app. All of them are free to use and will continue to work.

[4]: http://webpack.github.io/
[5]: https://gist.github.com/pthrasher/115cb52d5da254ad1ab5
[6]: https://gist.github.com/pthrasher/a641618d72fdcd616225


## Why This is Awesome

We have fully featured node, grunt, webpack builds for our new single page apps using many of the great tools available to the javascript community.  Development builds are quick and include tests, linting, rich error messaging, and live reload.

Our legacy code continues to be handled by Sprockets and adding a gem with a `.js` dependency can work as it does on any Rails project. Everyone is happy.
