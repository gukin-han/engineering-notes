---
date: 2026-05-20
tags: [backend, communication, sse]
---

# 12. Server Sent Events

So this is one of my favorite patterns.

I hesitated adding this as part of a design pattern, but I just fell in love when I first learned

about Server Sent Events that I thought, you know what, I think

this is elegant design.

You know, whoever designed Server Sent Events is brilliant.

You know, it's a pure HTTP thing, right?

It doesn't really work on other protocols.

But the fact that they moved HTTP from being a request-response into this streaming server-streaming

model is brilliant.

And the trick was: one request,

but the response is very, very, very long.

We are responding, but it doesn't have an end.

Basically, the response doesn't have an end.

That's basically what Server Sent Events are. If you send an HTTP request, you keep getting

data, data, you keep getting data, right.

And it just never ends.

And the trick here is the client understands these chunks, the streaming chunks, and parses the mini

responses, if you will, the messages in between.

Right.

So to the application, someone is making a request and it's streaming a bunch of responses that are never,

never ending using chunked streaming.

Right.

The content type.

But the client is smart enough to say, hey, you know what,

I'm actually going to parse these mini messages and I'm going to find my mini responses,

meaning messages, meaning events.

And that's the trick here.

Let's get into it.

Of course, there's always the limitation of request-response that forces us to do this stuff right.

The vanilla request isn't ideal for notifications in the backend.

The clients want real-time notifications from the backend, right?

If a user just logged in.

All right, if a message is received. Push works.

Definitely.

You can build WebSockets where the server physically pushes messages out of the blue to the

client.

That works, but it's restrictive.

And so Server Sent Events work with a request-response, and it also works with HTTP.

That means it works with vanilla.

Any HTTP server just supports it.

You don't even need to be a WebSocket server, so it works on the web and it's designed for it,

as we said.

So what is a Server Sent Event?

A response.

We talked about a response and a request always has a start and an end.

Right?

Client sends the request, the server sends.

The server sends logical events as part of the response.

So to the HTTP response, it didn't technically end.

We didn't send the final two lines

that end the response.

But we're sending effectively events in these chunks that the client is smart enough.

That is, by the way, a Server Sent Events object, EventSource object in the browser that understands this

and then just plays with that.

Right?

The server never writes the end of the response, the final response, if you will, but it sends these

mini events that can be parsed.

So if someone, let's say someone just logged in, you can respond with an event in this long response,

part of it.

And the client just parses this segment and oh, someone just actually logged in.

So did you get the trick?

It's very beautiful and it's still a request, but it's an unending response.

Effectively, that's how you can think about it.

Client parses the stream data looking for these events, and it works with the request-response initiative.

All right, let's take an example.

So Server Sent Events. I have a server that's just a normal web server.

At least it should be HTTP/1.1, otherwise it cannot work.

So this is, of course, because it doesn't support streaming.

Right.

And the client here sends a request, a special request with a special content type, and the server.

Well, what the server will do is it will actually respond with an event which is a bunch of bytes that has

a start and end.

Right.

That the client actually needs to understand.

It didn't technically finish writing the response yet because technically, if you write the response,

you can write part of the response, but you don't have to write the full response, right?

That's how TCP sockets work.

You can write another event in this case and then pauses.

The client can process these events and then finally it can close that whole connection.

So let me go with the pros and cons.

Real-time.

It is real-time, right?

Because the moment we get that, we will immediately.

It's like we have an open channel with the server that immediately sends us a feed of information and

it's compatible with the request-response model and with the HTTP model as well.

What's the cons?

The cons are that the client must be online, right?

Because you're sending a request.

The client has to be there to receive the responses, the mini responses we talked about.

Right.

And the client

might not be able to handle it.

It's kind of, if you think about it, the same problem as push, right.

If you're sending all these notifications, right, these messages, the client can disconnect.

It might not be able to handle the messages.

And what does the server do in this case?

Does the server save the state of the client, or the client actually was able to process this event but

not the server?

That's a lot of load on the backend.

Sometimes you don't want that.

Polling is preferred.

If you want lighter clients that are not sophisticated enough, you might want to go with a polling

approach, whether long or short.

But there is a problem also with Server Sent Events and we didn't really discuss this.

Maybe in the HTTP lecture

we're going to go into more details about this.

But

there is a limitation also of Server Sent Events when it comes to HTTP/1.1, you see.

Well, we'll discuss this more in the HTTP/2 lecture, but

there's a limitation in Chrome today.

If you connect to a domain, you can only establish six TCP connections to that domain.

That's it.

That's a limitation arbitrarily put by Chrome.

And I think a lot of browsers follow that.

Why?

Because

in HTTP/1.1

you can only send one request per connection.

And while this request is being processed, you cannot send anything else in that connection.

Right.

Unless you enable pipelining.

But it's a problematic thing.

So nobody does.

So you can only send one request.

And the moment you send the request, this connection is marked as busy.

And if the connections are busy, you cannot use them.

So what

about the rest of the HTTP requests?

Like I want my CSS, my JavaScript, my, you know, other files.

How do I download them?

Well, the browser actually opens six connections for you, up to six, and you can use those to send

requests.

But guess what?

What if all of these six are Server Sent Event requests?

What did we say?

Server Sent Events.

So Server Sent Events are a request that doesn't have an end.

The response is just being written.

So technically all the six connections are marked as busy.

So if you're connected to this domain and you have six channels,

like six requests of Server Sent Events, then

other requests will starve to the same domain.

You cannot even make a GET request, you cannot even fetch a JavaScript file because the connections

will be blocked because there is no.

It's pending.

We're going to show that in the connection pooling example.

Right.

That's why HTTP/2 is a better approach where we can have unlimited streams, where you can

multiplex one connection.

We can send multiple streams in the same connection.

We're going to send multiple streams in the same connection.

And I think the limit is like 200 and you can configure it, of course, right?

So that's one problem with Server Sent Events.

Be careful with it.

Right.

And again, it comes back to just understanding how things work.

Right?

By the end of the course, hopefully you will have a grasp of some understanding.

Don't memorize any of this stuff.

It just goes down to understanding how things work.

And naturally it will

click when things come into the picture.

Now let's do a demo for Server Sent Events.

All right, how about we show how Server Sent Events works?

So as we explained, Server Sent Events is a single request that the client makes and the server responds

with a special header.

You know, and this is the content type `text/event-stream`.

And if it responds with that, anything that it sends after that as just part of the body will be interpreted

by the client

as different streams.

Right.

As long as you start with the word `data` and you end with two new lines.

Right.

So the stream never finishes technically, but it's an unending response.

Right.

But we capture these events

that start with `data`, right,

colon and then these new lines.

How do we do this parsing?

Actually, the browser does it for us.

This is the client code.

If the client code is built, if you use this EventSource object in the browser which ships with every

browser and you connect to this endpoint, then you'll be able to capture every message that's sent.

So what this code does is it's an Express library that listens on port 8888.

It just returns when you visit the root returns this hello just to avoid CORS, cross-origin resource

sharing.

And then when you send a request to `/stream`, you get a single header.

And then after that I have a function here that's called send.

And this basically every second, it just sends back a data with a counter.

It's an event effectively, so that listens and does this stuff.

How about we actually run it and test it again?

I'm going to share all this code with you guys, but let's go ahead and run it.

And if I go to the browser right now and go to

localhost 8888.

Now we get the Hello.

Go to the console.

And what we're going to do is we're going to create a `const sse = new EventSource`.

And then you just point to the

directly to the stream.

localhost

8888 slash stream.

Right.

And then.

When you do that, right,

what happened is if you see the network, we actually sent a request and we actually got the

response.

And we're getting some data, but this data has not been captured

by the client.

See, if I go to the event stream, I can see it in the console, but nothing in the console here because

we never wired the event.

So what you do is `sse.onmessage` and then do a console log and then every time

you get a message,

you get a whole message

object.

Right.

So here's the data.

See, the data itself is just hello from server, remember?

If you go back here.

You have to actually send `data:` and then the string, right?

And that's how the boundary of the event is determined.

It's a little hacky, but who cares as long as it works, right?

But that's basically how Server Sent Events work.

Very elegant.

Very simple.

Right.

Yet

if I look at that, of course, it's a single request.

Looking at that, the response actually, look at this.

You see, even the dev tools told me this is an unending request.

See, caution, request is not finished yet.

All right.

So that's the trick.

I just absolutely love this trick.

I think it's very cool.

And I think there are a lot of applications for Server Sent Events.

Hope you enjoyed this.

I'm going to see you in the next lecture.
