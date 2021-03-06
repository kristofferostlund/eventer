# tiny-eventer

Tiny and simple event module with no third party dependencies.

## Usage

### Installation

Using npm:

```bash
npm install tiny-eventer [--save]
```

Using yarn:

```bash
yarn add tiny-eventer
```

In Node.js:

```javascript
'use strict'

// Load a global instance
var eventer = require('tiny-eventer');

// Or load the Eventer class to use a new instance of it.
var Eventer = require('tiny-eventer').Eventer;
```

In the browser:

```javascript
// eventer is a global object, attached on window.eventer
eventer === window.eventer;
// true

// Get the eventer class to for creating new instances
var Eventer = eventer.Eventer;
```

### API

#### `eventer.on(eventName, listener, [_this])`

Subscribes to events on `eventName`.

`eventName (String)` Name of the event to listen for

`listener (Function)` The function to be called. Any params passed into `trigger(eventName, [...params])` will be passed in.

`[_this (Any)]` A reference to `this` (or an ID of sorts) which can be used for unsubscribing to events when `listener` is an anonymous function. This can be used to group event listeners, as string or number could be passed in.

**Example**

```javascript
/** ... */

// Passing in a named function and skipping *_this*
eventer.on('user_added', onAddedUser);

function onAddedUser(user) {
  console.log('user_added event!');
}

// Passing in an anonymous function with a string ID as *_this*.
eventer.on('other_user_added', function (user) { console.log('other_user_added event!') }, 'some.id');

/** ... */
```

#### `eventer.once(eventName, listener, [_this])`

Subscribes to a single event on `eventName`. Only difference between `eventer.once` and `eventer.on` is that `eventer.once` will only fire once, as is suggested by the name of the function.

Events subscribed to using once are cancellable using `eventer.off(...)`.

`eventName (String)` Name of the event to listen for

`listener (Function)` The function to be called. Any params passed into `trigger(eventName, [...params])` will be passed in.

`[_this (Any)]` A reference to `this` (or an ID of sorts) which can be used for unsubscribing to events when `listener` is an anonymous function. This can be used to group event listeners, as string or number could be passed in.

**Example**

```javascript
/** ... */

// Passing in a named function and skipping *_this*
eventer.once('user_added', onAddedUser);

function onAddedUser(user) {
  console.log('user_added event!');
}

eventer.trigger('user_added', { userId: 1, name: 'Arthur Dent' });
// Logs 'user_added event!'

eventer.trigger('user_added', { userId: 2, name: 'Ford Prefect' });
// Logs nothing

/** ... */
```

#### `eventer.off(eventName, listener, [_this])`

Unsubscribes to events on `eventName`.

`eventName (String)` Name of the event to stop listening for

`[listener (Function)]` The function which would've been called. Either this `_this` can be used to match an event listener.

`[_this (Any)]` A possible other identification for when `listener` is an anonymous function, or if a group of events should be unsubscribed with a single call.

**Example**

```javascript
/** ... */

// Unsubscribe by passing in a named function and skipping *_this*
eventer.off('user_added', onAddedUser);

function onAddedUser(user) { /** ... */ }

// Unsubscribe by passing in a string ID as *_this* and leaving *listener* empty
eventer.off('other_user_added', undefined, 'some.id');

/** ... */
```

#### `eventer.clear(eventName)`

Unsubscribes all listeners on `eventName`

`eventName (String)` Name of the event to remove all listeners from

**Example**

```javascript
/** ... */

// Unsubscribe all listeners
eventer.clear('user_added');

/** ... */
```

#### `eventer.clearAll()`

Removes all listeners from all events

**Example**

```javascript
/** ... */

// Remove all listeners
eventer.clearAll();

/** ... */
```

#### `eventer.trigger(eventName, [...params])`

Triggers an event with the passed in parameters, if any.

`eventName (String)` Name of the event to trigger

`[...params (Any)]` Parameters to be passed into callbacks

```javascript
/** ... */

var newUser = {
  id: 42,
  name: 'Arthur Dent',
  occupation: 'Hapless Englishman'
};

var otherUser = {
  id: 49,
  name: 'Ford Prefect',
  occupation: 'Writer'
};

// Triggers an event with no parameters passed into it.
eventer.trigger('look_no_params_ma');

// Trigger event with a single parameter passed in (other than eventName, obviously).
eventer.trigger('user_added', newUser);

// Trigger event with multiple parameters passed in. Any number of parameters can be passed in.
eventer.trigger('users_added', newUser, otherUser);

/** ... */
```

#### `eventer.$events`

Container object for events, with keys as the name of the event and the values are arrays of event items (consisting of listener and _this)

#### `new eventer.Eventer()`

Creates a new Eventer instance useful for creating sandboxed events.

```javascript
/** ... */

var _eventer = new eventer.Eventer();

_eventer === eventer;
// false

_eventer instanceof eventer.Eventer;
// true

eventer instanceof eventer.Eventer;
// true

/** ... */
```
