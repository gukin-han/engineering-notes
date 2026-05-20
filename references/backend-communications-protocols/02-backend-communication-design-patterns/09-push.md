---
date: 2026-05-20
tags: [backend, communication, push]
---

# 09. Push

Let's talk about another design pattern when it comes to backend execution, and that's push. Very popular.

If you really want the response as fast as possible, if you really want results immediately in the client, then push is one of the famous patterns to implement on the backend.

It has, of course, its pros and it also has its cons.

And that's the purpose of this lecture.

Let's get into it.

So yeah, request-response isn't always ideal for certain workloads.

An example is real-time notification, right?

So what if someone just logged in?

What if someone just uploaded a YouTube video?

What if someone just uploaded a Twitter tweet?

How does the client know this event happened?

It's not like the client has to ask or write something in order to get the response. In this case, it doesn't work, right?

It's not something that the client has or knows about.

It doesn't have that knowledge. The server has that knowledge.

It's different. It's an event, right?

So with request-response you can do it.

You can just ask, "Hey, is there a notification? Is there a notification? Is there a notification?"

But that doesn't really scale well.

So what we're going to do is receive that message: "A user just logged in."

We talked about that.

But this is where the push model is great for certain use cases.

And this is perfect, right?

A chatting app is perfect for push notifications.

Push logic — where the server knows something, and is going to push it to the client.

Focus on the word "push."

Push means you're actually forcing.

And that could be good. Sometimes it could be bad, because a lot of clients actually cannot handle that load that you're pushing to them.

We'll come to that.

The push model is good for certain use cases.

Let's talk about that.

So what is push?

Here's how it works.

The client connects to the server.

The server sends data to the client.

The client does not have to request anything.

The only thing that needs to happen is there should be a connection established.

That's the medium on which you send data.

So it's almost like a unidirectional stream, but from the server side, so the client doesn't have to request anything.

In this case, to support this, a protocol must be bidirectional.

I can argue with that a little bit.

You can argue, hey, you can have a unidirectional protocol from one side.

Right.

But technically we have TCP.

TCP can work as a push, as effectively a bidirectional protocol is better in this particular case, and it's actually used by RabbitMQ.

And if we get a chance, we're going to show RabbitMQ in action here and how to do push notification effectively in RabbitMQ.

So in RabbitMQ, when you submit a message to the queue system, what happens is there are clients that consume this queue.

The model that RabbitMQ chooses, as opposed to Kafka, is that RabbitMQ will push the content of the queue at the moment we get a result in the queue.

The moment we get entries in the queue, they could be pushed to the clients that are connected to it.

So there is a connection established with the consumer and then these are pushed.

That's the model they chose for this.

So let's talk about this a little bit.

So a client — this is the timeline.

We have a server and we have a bidirectional connection, right?

So the connection is already established.

We're going to talk about how this looks like, actually.

So what happens here is the backend just gets a message out of the blue.

Right.

Because there are things happening on the other side.

Clients can send a message.

They can send requests.

They can upload files.

Right.

And there are other clients that are connected to this.

And the moment we get a message — someone uploaded a YouTube video — we need to push to all connected clients, push that result to those clients.

Now.

Imagine this is some sort of YouTuber like PewDiePie.

I have like 100 million subscribers, or Mr. Beast.

And imagine everyone has push notifications enabled by default — which is not the case, by the way. YouTube turned that off by default because they couldn't do it.

So it's impossible to do it.

Imagine every time I upload a video, I've got to notify 100 million users.

So the act of having fully-fledged connections all the time is impossible, first.

Second is, even if those are connected, then I have to loop through all of them and spend resources to actually push, push, push, push, push, push.

Right.

So YouTube does it.

But push notification is actually a very complex system. It's different — it's not like direct to consumer.

What YouTube does in this particular case is it actually pushes the notification to Apple or to the Android cloud, and they are responsible to push that notification down to their clients at their leisure.

And some other app events can happen here.

And another message can be pushed from the server, and we're going to see that gRPC actually supports this mode — the unidirectional from the server-side streaming.

And that's actually very interesting, right?

So the client didn't request anything.

It just gets data, and we're going to see how we can play tricks with this as well.

So we can have request-response actually work with push.

We'll see how. It's actually interesting.

So here are the push pros and cons.

So the pros of push are: it's real-time, and it's really good. The moment things happen, we get to push it.

That's the definition of push, because you're pushing the result.

You don't wait.

The moment you get the input, just push it to the client immediately.

Right. And pushing — think of it as: you have a connection, and then you're literally writing to the client socket.

It's not really magic, right?

We call it push because the result is pushed immediately at the moment the event is generated.

But there are a lot of disadvantages, to be honest, with this.

First of all, the client must be online to get anything pushed to it, right?

It has to be online.

When I say online, I mean it's physically connected to the server that is doing the pushing, right?

Otherwise, you cannot push something to a client that is offline.

Right?

You can push something to the server and then later on, when the client connects, do the pushing — but you cannot do it while they are offline.

Another disadvantage.

And that's basically why Kafka didn't do the push model like RabbitMQ.

We have no idea.

Pushing means, "Hey, here's data, here's data. I'm going to push it to you and I don't care."

Right.

That means the client has to be able to handle the load.

Right.

So what if the server is pushing tons of notifications, tons of messages to the client?

The server doesn't have knowledge, right? Aside from flow control at the TCP level, for example.

Whether the client can handle it or not, it doesn't know that, right?

It doesn't have that knowledge.

So if you push, push, push, push, push data, the client might be processing these messages, but it cannot keep up.

So you're forcing it. If you have a simple client, they might crash as a result of this, right?

That's why Kafka moved to something called long polling.

We're going to talk about it.

Right.

And it also requires a bidirectional protocol, right? In this case.

And this is where polling is preferred for lighter clients, so that the client pulls at their leisure.

They only make a request if they know that, "Hey, I actually can handle the load right now. Tell me if there's something happening."

And of course, if there's like one video being uploaded or one thing, you don't really care.

But if it's a volume — a large volume of data — it adds up.

Really adds up.

All right, guys, in the interest of time, I already spent some time to write a simple WebSocket application that does basically two participants or multiple participants chatting with each other.

Right.

And the moment a message is sent to the server, this message is pushed to all clients.

So this is an example of push notification.

And the reason is because WebSockets is actually a bidirectional protocol, because it uses the TCP link underneath it, right?

So it can support push.

And this is what we're going to do here in this case.

And we're going to have an entire section just for WebSocket.

So don't worry about that.

We're going to talk about WebSocket in detail in future lectures.

So let's get started.

Actually, this code is going to be available for you guys here.

So what we're going to do is use the Express library in Node.js.

We're going to get the WebSocket package, and then we're going to build an array of connections.

And these are our users.

Basically, every time someone connects to this server — which is what I'm building — we're going to add that connection to the array.

And then every time someone sends a message, we're going to loop through the connections and then push, push, push, push, push — send messages through those connections.

Very simple app.

So we're going to create the HTTP server web app — we're going to create the web server app, right?

The package here, and this is basically the actual library here.

And we're going to pass that server down to the WebSocket object so that we can establish the WebSocket handshake, which is the upgrade command.

Right.

So once this is done, we're going to listen on the HTTP server. And this doesn't matter — hey, I'm listening right now on port 8080, as we're going to do.

And this is where the beauty happens: when the WebSocket object actually receives a legitimate connection, an actual request for a WebSocket upgrade request — because you can connect all day through the TLS handshake.

But this WebSocket event will only execute if there is someone who sent a WebSocket connect request, and this happens.

You can do it from the client side through the browser using the WebSocket object in the browser.

That's what we're going to do as a client, right?

So on the client, we're going to spin up multiple browsers, and each browser is going to be a user effectively.

All right.

So sounds good.

Once we get a request, we're going to accept that connection, right?

And the moment we accept that connection, it becomes its own instance.

And this is where we're going to wire the event on this — hey, if someone sends a message, a WebSocket message in this case on this connection, go ahead and loop through all the connections and just turn around and send that — whoever sent that, like the remote port. I'm using the remote port as a unique identifier here, which is a very nice trick, because how do I identify users?

When you establish a TCP connection, there is always the source IP, the source port, the destination IP, the destination port.

The source port is always going to be unique, right?

Because that's the user who's connected.

And I'm using that as a unique identifier for the user here — whoever the connected user's remote port is.

And the message — we're going to just print the message and then just tell everybody that's connected.

Once we're done with that, we're going to push that connection to the array.

Hey, because we just got a new user, right?

This will only execute if someone just connected, right?

And then also mine as well.

We're going to tell everybody that someone just connected as well.

And that's another loop.

Say, "Hey, user blah just connected."

So very, very simple server.

Let's go ahead and run this in debug mode.

Now we have our chatting web server running.

Let's go ahead and spin up some browsers and do some push notifications.

How about that?

All right.

Now I think I can safely disappear here.

Let's go and fire up my dev tool right here.

I've got to fire up dev tool right here, and then we're going to play a client here.

All right?

So in my first user, I'm going to declare a variable called `ws` equal to `new WebSocket` in the browser.

And the protocol — the server is localhost and the port is 8080.

And that's it.

Just doing that, we just connected.

And that will effectively call the request command here.

And so what I'm going to do also — on message, if someone sent me a message, I am going to print that message.

That's the only two pieces of information that we require.

So we have one here.

And just so we can actually be on the same page, on the second call, I'm going to do a breakpoint here so that I know that someone connected.

I just want to show you what happens.

That is actually — this gets called.

There's no point. We can just copy and paste.

Right.

But the moment we do that, immediately a request is created and a connection object is created for us.

And the socket here will have the remote port, which is 64876 in this case.

So the next line will be wiring up the message.

So in case someone sent a message, call this function, right.

And then add the connection to the array.

And then now we can notify everybody who has actually connected to let them know that this guy just connected.

And this is the loop.

How many lines do we have?

We have two actually.

So I'm going to notify myself too.

So that's fine.

Let's go ahead and run it.

Go back.

Look at that.

We got that — user 64876 just connected, but we didn't get it here.

That's easy, right?

Because we never actually wired the event.

It was so fast.

We never actually managed to wire the event.

So let's go ahead and just wire it up.

And of course it's too late.

We didn't capture it.

That's fine.

But let's say this client is going to say, "Hey, Dawson."

Hey.

Now this client actually sent the message.

So now this is me — 64648. This 64876 is the other one.

So we got back the message that we said to ourself, right.

But this guy also got it.

So that's kind of a push.

Let's actually send a message from this guy.

Hi.

You notice that when we sent the "hi" message here — that's me, I got it. Everybody gets this basically immediately. The message goes to the server and the server just rebroadcasts it to everyone.

So that's a push — almost a push model, right?

And now we're chatting.

We can send more commands here.

"What's up?"

Immediately we get the "What's up?"

Of course.

And everyone else.

I guess we can write server-side logic for me not to get the message here, but actually this works with not just one client or two clients.

Actually, it works with multiple.

Maybe this will make it a little bit more clearer.

A new client will connect right now.

Let's steal the code here.

We got this event executed.

We no longer need this breakpoint.

Go back.

Notice 94 connected. 94 just connected.

Doth send. Hey, all.

So 94 says "Hey," 94 says "Hello," and then you can chat.

You can easily add a button — an HTML page that does the chatting — but this is basically the gist of push notification.

You guys, you need a bidirectional protocol, right.

So if you can build a unidirectional from the server, you can also have push notifications.

But that's as opposed to making a request with curl or any other client to say, "Hey, do I have a message? Do I have a message?"

This way you get pushed the result, and the pushing happens to be exact right here, right?

It's just nothing about a sent message.

But the trick here is as you give the event that someone just sent a message — because this will get executed.

By the way, if there is a message, because remember, everyone is connected to the server, right?

Nobody is actually connected to each other.

Technically, all of these clients are — it's a centralized model — connected to one central server, and the server gets the message and is responsible to just repeat the message to every other connected client.

Of course.

Is this code perfect?

Of course not, because if some of these clients get disconnected, the server needs to know how to skip it and pop it out of the connection.

All right.

Let's try this.

What would happen if I disconnect it?

Is that close?

I think I just closed.

Right.

So now what happens?

If I say — I think this will break the server.

But yeah, it might actually crash the server, because now we're looping and sending — someone send a connection to a connection that is already closed.

Right?

So yeah, actually no one got the message.

So because I think the server didn't really crash, we can actually debug.

It's all part of the test.

Let's do that.

Do we get a message to see?

Do we get an event reader?

So we got here.

We still have three connections despite one being disconnected.

Right.

And if we look at the state — which was the first user — was disconnected.

Right.

So let's find — user zero got disconnected.

I bet that this `close` — the connected is false.

See?

So now what we need to do is actually add just a nice `if` statement.

If connected, then send it.

That's easy, but still it's a waste to loop through things that are already disconnected.

Right.

You can do like a map here or things like that to fix this bug.

It should be easy.

Just do a `c.connected`.

Right.

Do that.

Right.

So that's another thing.

I'll let you do this stuff.

It's just the nitty-gritty stuff.

All right, guys, that was the push model.

Hope you enjoyed it.

See you in the next lecture.
