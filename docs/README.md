# Publish Subscribe

JavaScript's implementation of the Publish-Subscribe pattern.

[![npm](https://badgen.net/npm/v/@r37r0m0d3l/publish_subscribe?&icon=npm&label=npm&color=DD3636)](https://www.npmjs.com/package/@r37r0m0d3l/publish_subscribe)
[![downloads](https://badgen.net/npm/dt/@r37r0m0d3l/publish_subscribe?&icon=terminal&label=downloads&color=009688)](https://github.com/r37r0m0d3l/publish_subscribe)
[![stars](https://badgen.net/github/stars/r37r0m0d3l/publish_subscribe?&icon=github&label=stars&color=ffcc33)](https://github.com/r37r0m0d3l/publish_subscribe)
[![types](https://badgen.net/npm/types/@r37r0m0d3l/publish_subscribe?&icon=typescript&label=types&color=1E90FF)](https://github.com/r37r0m0d3l/publish_subscribe)
[![build](https://badgen.net/travis/r37r0m0d3l/publish_subscribe?&icon=travis&label=build)](https://github.com/r37r0m0d3l/publish_subscribe)
[![lgtm](https://badgen.net/lgtm/grade/g/r37r0m0d3l/publish_subscribe?&icon=lgtm&label=lgtm:js/ts&color=00C853)](https://github.com/r37r0m0d3l/publish_subscribe)

---

*If you use this project don't forget to give a
⭐ [star](https://github.com/r37r0m0d3l/publish_subscribe) ⭐ to it on GitHub!*

---

## 🏃💨 Tl;dr

```typescript
import { PublishSubscribe } from "@r37r0m0d3l/publish_subscribe";
//
const pubsub: PublishSubscribe = new PublishSubscribe();
type IChannel = number|string|symbol;
const CHANNEL: IChannel = "app";
//
const isCalledOnce: boolean = false;
function syncCallback(data: any, channel: IChannel, token: string): any {
  return true;
}
async function asyncCallback(
  data: any,
  channel: IChannel,
  token: string
): any {
  return true;
}
const tokenSync: string = pubsub
  .subscribe(CHANNEL, syncCallback, isCalledOnce);
const tokenAsync: string = pubsub
  .subscribe(CHANNEL, syncCallback, isCalledOnce);
//
const data: any = { someData: "to publish" };
const resultOnly: boolean = true;
const cloneData: boolean = true;
function callback(
  results: Array<any | { channel: IChannel; data: any; token?: string }>
): any {}
const resultsSync = pubsub
  .publishSync(CHANNEL, data, resultOnly, cloneData, callback);
(async () => {
  const resultsAsync = await pubsub
    .publishAsync(CHANNEL, data, resultOnly, cloneData);
})();
```

## 📦 Usage

Installation.

```bash
npm install @r37r0m0d3l/publish_subscribe
```

CommonJS.

```js
const { PublishSubscribe } = require("@r37r0m0d3l/publish_subscribe");
```

ECMAScript Modules.

```js
import { PublishSubscribe } from "@r37r0m0d3l/publish_subscribe";
```

UNPKG CDN.

```html
<script src="
https://unpkg.com/@r37r0m0d3l/publish_subscribe/dist/publish_subscribe.min.js
"></script>
```

## ⌨ Creating Instance

```js
const pubsub = new PublishSubscribe();
```

## ℹ Logging

Set log function.

```js
pubsub.setLogging((info) => {
  console.group("LOGGING!!!");
  console.log(info);
  /*
  Example when subscribing:
  {
    channel: "app",
    callback: [Function: sync],
    once: false,
    method: "subscribe"
  }
  Example when publishing:
  {
    channel: "app",
    data: { data: "the data" },
    token: "CETl6gZXkTMhm7JY",
    method: "publishSync -> send"
  }
  */
  console.groupEnd();
});

pubsub.disableLogging();
```

## 📛 Naming Channels

Channels can be finite numbers, strings or symbols.

```js
const CHANNEL_NAME_AS_STRING = "TheChannelName";
const CHANNEL_NAME_AS_NUMBER = 123;
const CHANNEL_NAME_AS_SYMBOL = Symbol("ThisChannelLookVeryPrivate");
const CHANNEL = "channel_name";
```

## 📩 Subscription

Synchronous subscription.

```js
const tokenOfSynchronous = pubsub
  .subscribe("channel_name", function sync(data, channel, token) {
    return {
      message: "Here can by any kind data for synchronous functions"
    };
  });
```

Asynchronous subscription.

```js
const tokenOfAsynchronous = pubsub
  .subscribe("channel_name", async (data, channel, token) => {
    // Other async call here
    return "Here can by any kind data for asynchronous functions";
  });
```

Reusable subscription.

```js
/**
 * @param {*} data
 * @param {number|string|Symbol} channel
 * @param {string} token
 */
function synchronousCallback(data, channel, token) {
  // And here is no any data returned if you wish
}
const tokenOne = pubsub.subscribe("channel_name_1", synchronousCallback);
const tokenTwo = pubsub.subscribe("channel_name_2", synchronousCallback);
```

No need for a token as the subscription will expire after a first successful call.

```js
pubsub.subscribeOnce("channel_exit", (data, channel, _token) => {});
```

Emit publish when the subscription happened.

```js
pubsub.onSubscribe("channel_name", { message: "This message for every new subscription" });
```

To disable this.

```js
pubsub.onSubscribeClear("channel_name");
```

## 📨 Publishing Events

Intercept all publishing.

```js
pubsub.onPublish((channel, data) => {
  // All publishing will be intercepted here
  // and you can do your "message matching" here
  switch(channel) {
    case "app:logout":
      pubsub.dropAll();
    break;
  }
});

pubsub.onPublish(); // now its disabled
```

Get synchronous results from subscribers.

```js
// Channel name
const channel = "app:start";
// Any kind of data - primitive or object
const data = { data: "the data" };
// Default behavior
const resultOnly = true;
// Prevent published data changes between subscriptions
const cloneData = true;
// Array with results
const results = pubsub
  .publishSync(channel, data, resultOnly, cloneData, (results) => {
    // Only synchronous functions will be called!
    // Array of results can be intercepted in callback
    console.log(results);
  });
console.log(results); // or can be accessed as function call
```

Get synchronous and asynchronous results from subscribers.

```js
pubsub
  .publishAsync("channel_name", { data: "the data" }, false)
    .then((results) => {
      // results here also contains
      // additional array of data from subscribers
      // [ { channel: "app", result: "data", token: "zzroUP97lnxL0VUa" } ]
    });
```

Do not wait for anything from subscribers - use `publish`.

```js
pubsub.publish("channel_name", { data: "the data" });
```

Create `sticky` data for the channel. Set `sticky` to **TRUE** is the same as calling `onSubscribe` after `publish`, `publishAsync`, `publishSync`.

```js
const channel = "channel_name";
const data = { data: "the data" };
const cloneData = false;
const sticky = true;
pubsub.publish(channel, data, cloneData, sticky);
```

## 📪 Unsubscribe

Unsubscribe via token previously retrieved from the `subscribe` method.

```js
const token = pubsub.subscribe("channel_name", () => {});
pubsub.unsubscribeByToken(token);
```

Unsubscribe by channel and callback.

All subscriptions in `channel` based on `callback` will be removed.

```js
// callback == function someFunction(data, channel, token) {}
pubsub.unsubscribeByChannelAndCallback("channel_name", callback);
```

This `callback` function will be removed from **all** subscriptions.

```js
pubsub.unsubscribeByCallback(callback);
```

Unsubscribe based on the parameters provided.

```js
pubsub.unsubscribe(callbackOrChannelOrToken);
pubsub.unsubscribe(channel, callback);
```

## 🛠️ Utilities

Retrieve callback function via the token.

```js
// token == "zzroUP97lnxL0VUa"
const callback = pubsub.getCallback(token);
```

Has any subscriptions.

```js
pubsub.hasSubscription("channel_name");
```

Has channel.

```js
pubsub.hasChannel("channel_name");
```

Get a list of channels. An array of numbers, strings, symbols.

```js
pubsub.getChannels();
```

Drop channel.

```js
pubsub.dropChannel("channel_name");
```

Clear instance.

```js
pubsub.dropAll();
```

Potential web browser memory leak fix.

```js
globalThis.addEventListener("beforeunload", function () {
  pubsub.dropAll();
});
```

## 📖 Examples

You can look for more examples in the [repository folder](https://github.com/r37r0m0d3l/publish_subscribe/tree/master/examples).

### WebSocket

#### Server

```js
const app = require("express")();
const http = require("http").createServer(app);
const io = require("socket.io")(http);
http.listen(3000, function() {
  console.log("listening on *:3000");
});
io.on("connect", function(socket) {
  socket.on("event_from_client", function(data) {
    socket.emit("event_from_server", { data: "Hello Client" });
  });
  socket.on("disconnect", function() {
    console.log("[disconnect]");
  });
});
```

#### Client

```js
import io from "socket.io-client";
import { PublishSubscribe } from "@r37r0m0d3l/publish_subscribe";
const socket = io("ws://localhost:3000");
const pubsub = new PublishSubscribe();
pubsub.onPublish((channel, data) => {
  const [root, ...breadcrumbs] = channel.split("/");
  switch (root) {
    case "websocket":
      console.log({ channel, data });
      break;
  }
});
socket.on("connect", function() {
  pubsub.publish("websocket/connect");
});
socket.on("close", function() {
  pubsub.publish("websocket/close");
});
socket.on("event_from_server", (data) => {
  pubsub.publish("websocket/event_from_server", data);
});
pubsub.subscribe("websocket/event_from_client", () => {
  socket.emit("event_from_client", { data: "Hello Server" });
});
pubsub.subscribe("websocket/connect", () => {
  pubsub.publish("websocket/event_from_client", { data: "Hello Client" });
});
```

### Redis

```js
const redis = require("redis");
const { PublishSubscribe } = require("@r37r0m0d3l/publish_subscribe");
const CHANNEL = "main";
const globalPubSub = new PublishSubscribe();
const publisher = redis.createClient();
const subscriber = redis.createClient();
globalPubSub.subscribe("redis:start", function() {
  globalPubSub.publish(`redis:publish:${CHANNEL}`, "First Message");
});
subscriber.on("subscribe", function() {
  globalPubSub.publish("redis:start");
});
subscriber.on("message", function(channel, message) {
  let data = message;
  try {
    data = JSON.parse(message);
  } catch (err) {
    //
  }
  globalPubSub.publish(`redis:channel:${CHANNEL}`, data);
});
globalPubSub.subscribe(`redis:publish:${CHANNEL}`, function(message, channel) {
  const redisChannel = channel.split("redis:publish:").pop();
  let redisMessage = message;
  if (typeof message !== "string") {
    redisMessage = JSON.stringify(message);
  }
  publisher.publish(redisChannel, redisMessage);
});
subscriber.subscribe(CHANNEL);
globalPubSub.subscribe(`redis:channel:${CHANNEL}`, function(message, channel) {
  console.dir(message);
});
globalPubSub.publish(`redis:publish:${CHANNEL}`, "Third", undefined, true);
```

## 🤷 About

Yet another publish-subscribe library? Why this library exists:

-   ⭐⭐⭐

    -   Preventing published data changes between subscriptions.

    -   Possibility to publish events asynchronously.

    -   Possibility to do synchronous publishing and receive data from subscriptions.

    -   Possibility to use async callbacks in subscriptions.

    -   Get subscription callback function by token and use it somewhere else.

    -   Error swallowing. The publish-subscribe pattern should not break on some function somewhere deep in code.

-   ⭐⭐

    -   Possibility to use finite numbers, strings or symbols as channel names.

    -   Subscription functions receive callback token among channel names and published data.

    -   Has *subscribeOnce* method.

    -   Has *onPublish* method for *global message matching*.

-   ⭐

    -   Custom logging into the console for most actions.

    -   TypeScript definitions.

    -   Almost dependency-free. This library will work even if the last commit was ten years ago.

The **worst** things that can happen to the publish-subscribe library that is **not** here:

-   ☠️☠️☠️

    -   You should have **multiple subscriptions** for one channel. Somehow it does not exists in other libraries.

    -   Event inheritance. This should be handled by subscription callbacks **not by** the publish-subscribe system.

    -   Hellish **wildcards-hierarchical addressing-messages matching** for each event.
    First of all, this is impossible with numbers or symbols.
    And this makes no sense as you can create the root channel name anyway.
    In example when you publish `"app:user:registered"` and `"app:user:login"`, just publish `"app:user"` among others.
    While when you publish `"app:user:re-login"`, do not publish `"app:user"` and `"re-login"`
    event will be kept *private* or *unimportant* to others.

-   ☠️☠️

    -   Possibility to define **context for each callback**.
    You have arrow functions for that.
    You should not save context in one place and take it hostage.
    Probably you should use global variables instead etc.

    -   **Cancel publish** event distribution for subscribers. This behavior reserved for Observer pattern.

-   ☠️

    -   No **order priority** for subscriptions. Somehow pattern should be about decoupling, but sometimes it has it in.

    -   **Sticky events** concept.
    Events will **stick** in and if any subscriber subscribes for such events after they were published,
    the subscribers will still receive them upon registration.

The things you may not like:

-   🔌🔌🔌

    -   No ECMAScript 3 / ECMAScript 5 / Internet Explorer 6 compatibility.
    You can transpile the library to the CommonJS module via Babel with the configuration you need.

## 👀 Discover more

| URL | Description |
|:---|:---|
| [jsonsort.r37r0m0d3l.io](https://r37r0m0d3l.github.io/json_sort) | Neat online JSON sorter |
| [consono.js.org](https://consono.js.org) | The most informative & correct variable inspector |
| [of.js.org](https://of.js.org) | Promise wrapper with some sugar |
| [publish-subscribe.js.org](https://publish-subscribe.js.org) | Implementation of the Publish-Subscribe pattern |
| [vicis.js.org](https://vicis.js.org) | Present & transform for JSON in REST API |
| [npmjs.com/fallback-local-storage](https://npmjs.com/package/fallback-local-storage) | Universal localStorage fallback |
| [npmjs.com/@hilesystem](https://npmjs.com/package/@hilesystem/local) | Filesystem common function wrappers |
| [npmjs.com/@corefunc](https://npmjs.com/package/@corefunc/corefunc) | "Don’t repeat yourself" collection of functions |
