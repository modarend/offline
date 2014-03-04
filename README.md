Offline
======

**Note to users pre-0.6.0:  Offline previously used a cloudfront hosted file as one of it's methods of detecting the connection status.  This method is now deprecated.  If your app is getting cloudfront 404s, please upgrade to Offline 0.6.0.**

Improve the experience of your app when your users lose connection.

- Monitors ajax requests looking for failure
- Confirms the connection status by requesting an image or fake resource
- Automatically grabs ajax requests made while the connection is down and remakes them
  after the connection is restored.
- Simple UI with beautiful themes
- 3kb minified and compressed

Installing
----------

Include [the javascript](https://raw.github.com/HubSpot/offline/v0.7.0/offline.min.js) and one of [the themes](http://github.hubspot.com/offline/docs/welcome/) on your site.  You're done!

Advanced
--------

Optionally, you can provide some configuration by setting `Offline.options` after
bringing in the script.

Options (any can be provided as a function), with their defaults:

```javascript
{
  // Should we check the connection status immediatly on page load.
  checkOnLoad: false,

  // Should we monitor AJAX requests to help decide if we have a connection.
  interceptRequests: true,

  // Should we automatically retest periodically when the connection is down (set to false to disable).
  reconnect: {
    // How many seconds should we wait before rechecking.
    initialDelay: 3,

    // How long should we wait between retries.
    delay: (1.5 * last delay, capped at 1 hour)
  },

  // Should we store and attempt to remake requests which fail while the connection is down.
  requests: true,

  // Should we show a snake game while the connection is down to keep the user entertained?
  // It's not included in the normal build, you should bring in js/snake.js in addition to
  // offline.min.js.
  game: false
}
```

Properties
----------

`Offline.check()`: Check the current status of the connection.

`Offline.state`: The current state of the connection 'up' or 'down'

`Offline.on(event, handler, context)`: Bind an event.  Events:

  - up: The connection has gone from down to up
  - down: The connection has gone from up to down
  - confirmed-up: A connection test has succeeded, fired even if the connection was already up
  - confirmed-down: A connection test has failed, fired even if the connection was already down
  - checking: We are testing the connection
  - reconnect:started: We are beginning the reconnect process
  - reconnect:stopped: We are done attempting to reconnect
  - reconnect:tick: Fired every second during a reconnect attempt, when a check is not happening
  - reconnect:connecting: We are reconnecting now
  - reconnect:failure: A reconnect check attempt failed
  - requests:flush: Any pending requests have been remade
  - requests:hold: A new request is being held

`Offline.off(event, handler)`: Unbind an event

Checking
--------

By default, Offline makes an XHR request to load your `/favicon.ico` to check the connection.  If you don't
have such a file, it will 404 in the console, but otherwise work fine (even a 404 means the connection is up).
You can change the URL it hits (an endpoint which will respond with a quick 204 is perfect):

```javascript
Offline.options = {checks: {xhr: {url: '/connection-test'}}};
```

Make sure that the URL you check has the same origin as your page (the connection method, domain and port all must be the same), or you
will run into CORS issues.  You can add `Access-Control` headers to the endpoint to fix it on modern browsers, but it will still cause issues on
IE9 and below.

If you do want to run tests on a different domain, try the image method.  It loads an image, which are allowed to cross domains.

```javascript
Offline.options = {checks: {image: {url: 'my-image.gif'}, active: 'image'}}
```

The one cavet is that with the image method, we can't distinguish a 404 from a genuine connection issue, so any error at all will
appear to Offline as a connection issue.

Reconnect
---------

The reconnect module automatically retests the connection periodically when it is down.
A successful AJAX request will also trigger a silent recheck (if `interceptRequests` is not false).

You can disable the reconnect module by setting the `reconnect` to false.  Reconnect can be
configured by setting options on the reconnect setting.

Requests
--------

The requests module holds any failed AJAX requests and, after deduping them, remakes them when the connection
is restored.

You can disable it by setting the `requests` setting to false.

Dependencies
------------

None!

Browser Support
---------------

Modern Chrome, Firefox, Safari and IE8+
