# [Chaplin.Layout](../src/chaplin/views/layout.coffee)

`Chaplin.Layout` is the top-level application 'view'. It doesn't inherit from `Chaplin.View` but borrows some of its functionalities. It is tied to the `document` dom element and register app-wide events, such as internal links. And mainly, When a new controller is activated, `Chaplin.Layout` is responsible for changing the main view to the view of the new controller.

## Methods of `Chaplin.Layout`

### initialize([options={}])

* **options**:
    * **routeLinks**: the selector of elements you want to apply internal routing to. Set to false to deactivate internal routing. *Default: 'a, .go-to'*. If `false`y, chaplin won’t route links at all.
    * **skipRouting**: if you want to skip the internal routing in some situation. Can take the following value:
        * selector: check if the activated link matches the selector.
        * function: check the return value. Return `true` to continue routing, return `false` to stop routing. The path and the elements are passed as parameters. Example: `function(href, el) { return href == 'bla'; }`
        * false: never skip routing
    Default: '.noscript'*. That is, you can add a `noscript` class to internal links to prevent routing by the Chaplin application.
    * **openExternalToBlank**: whether or not links to external domains should open in a new window/tab. *Default: false*
    * **scrollTo**: the coordinates (x, y) you want to scroll to on view replacement. Set to *false* to deactivate it. *Default: [0, 0]*
    * **titleTemplate**: a function which returns the document title. Per default, it gets a object passed with the properties `title` and `subtitle`. *Default: _.template("<%= subtitle %> - <%= title %>")*


### delegateEvents([events])

A wrapper for `Backbone.View.delegateEvents`. See Backbone [documentation](http://backbonejs.org/#View-delegateEvents) for more details.


### undelegateEvents()

A wrapper for `Backbone.View.undelegateEvents`. See Backbone [documentation](http://backbonejs.org/#View-undelegateEvents) for more details.


### hideOldView(controller)

Hide the active (old) view on the `beforeControllerDispose` event sent by the dispatcher on route change and scroll to the coordinates specified by the initialize `scrollTo` option.


### showNewView(context)

Show the new view on the `dispatcher:dispatch` event sent by the dispatcher on route change.


### adjustTitle(context)

Adjust the title of the page base on the `titleTemplate` option. The `title` variable is the one defined at the application level and the `subtitle` the one at the controller level.


### openLink(event)

Open the `href` or `data-href` URL of a DOM element. When `openLink` is called it checks if the `href` is valid and runs the `skipRouting` function if set by the user. If the href valid, it checks if it is an external link and depending on the `openExternalToBlank` option, opens it in a new window. Finally, if it is an internal link, it starts routing the URL.

## Usage

### App-wide events

To register app-wide events, you can define them in the `events` hash. It works like `Backbone.View.delegateEvent` on the `document` dom element.


### Route links internally

If you want to route links internally, you can use the `events` hash with the `openLink` function like so:

```coffeescript
# CoffeeScript
events:
  'click a': 'openLink'
```

```javascript
// JavaScript
events: {
  'click a': 'openLink'
}
```

To open all external links (different hostname) in a new window, you can set `openExternalLinksInNewWindow` to true when initializing `Chaplin.Layout` in your `Application`:

```coffeescript
# CoffeeScript
class MyApplication extends Chaplin.Application
  initialize: ->
    # ...
    @initLayout openExternalLinksInNewWindow: true
```

```javascript
// JavaScript
var MyApplication = Chaplin.Application.extend({
  initialize: function() {
    // ...
    this.initLayout({openExternalLinksInNewWindow: true});
  }
});
```

To add a custom check whether or not a link should be open internally, you can override the `isExternalLink` method:

```coffeescript
# CoffeeScript
class Layout extends Chaplin.Layout
  isExternalLink: (href) -> # some test on the href variable
```

```javascript
// JavaScript
var Layout = Chaplin.Layout.extend({
  isExternalLink: function(href) {} # some test on the href variable
});
```

### View loading

There is nothing to do, the Layout is listening to the `beforeControllerDispose` and `dispatcher:dispatch` and will trigger the function when a new route is called. If you are not happing with the site scrolling to the top of the page on each view load, you can set the `scrollTo` option when initializing `Chaplin.Layout` in your `Application`:

```coffeescript
# CoffeeScript
class MyApplication extends Chaplin.Application

  initialize: ->
    # ...
    @initLayout
      scrollTo: [10, 30] # will scroll to x=10px and y=30px.
      # OR
      scrollTo: false    # deactivate the scroll
```

```javascript
// JavaScript
var MyApplication = Chaplin.Application.extend({
  initialize: function() {
    // ...
    this.initLayout({
      scrollTo: [10, 30] // will scroll to x=10px and y=30px.
      // OR
      scrollTo: false    // deactivate the scroll
    });
  }
});
```
