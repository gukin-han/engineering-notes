---
date: 2026-05-20
tags: [backend, communication, stateful, stateless]
---

# 15. Stateful vs Stateless

I wanted to take this lecture with a grain of salt because it's a very contentious topic in the engineering space, and that's stateful versus stateless or stateless versus stateful.

And the definition game really doesn't matter.

You guys, it's just what is the side effect of things.

And that's what matters to us engineers.

So the entire lecture, or the entire course for that matter, never hone your mind into the definition that, "Oh, this must be this, because the definition is this."

It's pointless, right?

And I suppose this is kind of a theoretical, philosophical discussion, if you will.

But I think it's very important to understand why are we doing things to begin with.

Stateless versus stateful is the style of storing state within an application.

And to me, at least, relying on that state being there — otherwise things break.

That's what the definition of stateless is, and stateful.

And you can take this to a system.

You can take this to a backend, you can take this to a function, really, and you can take this to a protocol.

A protocol can be stateless or stateful.

It all depends on how you perceive things.

Let's get into it.

So, stateful versus stateless backends.

What is stateful?

A stateful backend is a backend that stores state about its clients in its memory.

Not only that — it actually depends on that information being there for it to function properly.

And that's a big difference between stateful to anyone, because you store information all the time.

You are, of course, going to have a variable, right?

Is storing a variable state?

Of course it's a state, right?

And that's where the definition games go amok.

But having that information being there and relying on it being stateful is basically — that's the copout really here.

The problem, and stateless is, the client — there is no state stored in the server at all.

The client is responsible to transfer the state with every request.

So we are always — in case the backend crashes — I can always get back to that state without needing to remember that state, if you will.

Right.

You may store some state in memory, some state even in disk, but it's okay if you lose it, because you don't really rely on anything being there.

Right.

Let's say you store something and you lost it, and the disk corrupts and you start back again.

If you rely on that particular thing, then you're kind of stateful.

But technically, a stateful app can still store something outside of its application somewhere else, like a database, right?

So if you look at it this way, then the entire architecture is stateful, because there is a state store that we're relying on.

But the app itself is stateless.

Why?

Because I can crash the app and I can start a brand new app from scratch, and that app will work because, yeah, I will read from another application, but that's the responsibility of another app, right?

Does that make sense?

So you spin back up and then that state is gone, effectively.

It's okay if it's gone.

I'm not relying on anything.

I'm completely stateless in this particular case.

And so let's talk about some examples of stateless backend.

This is — backend can store data somewhere else, right?

So that's what we talked about.

Yeah.

Even if I'm a backend that is stateless, I can still store some data somewhere else.

And I don't really rely on that data even to be there, and it says, "Hey, I'm just moving along," like logs.

Like if you're logging something, you turn it on, you take the data, you store it in that database, and then even if you turn it off and turn on, it doesn't matter.

You're stateless.

Can you restart?

Here's another thing that I always ask people to do.

And that's how — what tells you if your backend is stateless again.

I'm looking at just the backend here.

Just that piece.

You have many applications running, many services.

But looking just at the backend application you own, can you restart that backend if it's idle?

Of course.

And then you start the backend up, and can the clients that were connected before finish their workflow without actually breaking, as if nothing happened?

If that's the case, your application is stateless.

If that's not the case, then there is a state store somewhere that you lost, and the clients were relying on that being there.

And that's the problem.

So what makes a backend stateless — a stateless backend can store state somewhere else in the database.

So we talked about that, and the backend remains stateless, but the system technically is still stateful, right?

You can absolutely have something like that.

The whole system is stateful because there is a database, right?

Of course I need to store my data.

If that data is gone, then technically I'll be broken.

Right?

But my application in itself, it can still serve requests by itself.

It is stateless.

There is no state stored in that application itself.

Right.

And then same thing.

Can you restart the backend during the idle time, and the client workflow can continue to work?

If you can do that, you're a stateless backend, and don't think of this like it's a medal you take, like, "Oh, my application is stateless, I'm good," right?

It's not really a badge of honor or something to attain to — it's just a state, right, at the end of the day.

And there are disadvantages and advantages for both, right.

Stateful and stateless.

Right.

And if you're willing to take the hit, you can actually play some tricks to achieve that.

So a stateful backend, for example — let's say I built a login application where my user visits a page called Login with user and password, and then the backend turns around and talks to Postgres or a database and verifies that the username and password is correct.

It responds back, and then the backend will generate a session ID, session1, and it returns to the user that session ID and stores it locally.

It stores the session right here.

It does not store the session in the database — it stores it right here, because, hey, I want it to be a stateful in this case.

I return the session, the next call for the profile, for example.

"Hey, I want to view the profile."

And of course, if session1 — we set the cookie.

The cookie will send session1 with every subsequent request, because the cookies were sent.

In this particular case, the session is there.

What the backend will do is, "Hey, let's check. I got the cookie session1. Is session1 in memory? Is it in my application?"

If I have it in my memory, that means I already authenticated this user before.

I know he's good.

Everything has been authenticated before.

It's good.

So I'm going to go ahead and actually return the user profile, right, in this case.

But what does that actually break?

Here's where it breaks.

If, effectively, let's say we restarted that backend, that session is gone.

We never stored it locally — well, we stored it only locally.

So the beauty here is we never actually relied on the database to check if the session is valid or not.

Right.

You could have done that, but you did it this way because, hey, I just want to quickly check here.

I want to save a trip to the database.

I don't want to do that trip.

I wanted to check locally.

Right.

It's how caches work in this particular case.

But let's say I restart my backend, it crashes.

What happens is, if the user actually refreshed their page, that request will send the session1, right?

It will send session1, the backend will check it.

And session1 is not there.

It will assume that, "Hey, I don't know you, alright? I don't know. The session ID just exit."

Right.

And it will actually respond back with the login.

You remember these days, back in the nineties, at least you would log in.

And you would log in and press submit, the page reloads.

Fine.

Right.

But the moment you refresh your profile.

Right.

You would get back to the login page again.

Sometimes it works.

Sometimes you get back to the profile — why? — because your request happened to hit a backend that did have it.

Because that's what — when you develop an application, you develop it in a single machine.

So you never see this problem, because you will always hit the same backend.

Right.

But the moment you do load balancing, right, then what happens is sometimes you go into this server and the session is valid, sometimes you hit this server and the session is not valid, and it will give you the problem.

And that's why — sticky sessions.

Sometimes stateful applications require the stickiness.

You configure your load balancer to have sticky connections to it, and sometimes it's required to do a sticky session or sticky load balancing, where all the requests from this client go to the same backend.

Sometimes you're required to do this.

Actually, while I was building a gaming app, I had to do that to minimize the number of records to the database, just for a short period of time, so you can play with these things.

So the workflow in this case, the user has broken it like, "Why, why are you forcing me to log in again?", right?

So how do you solve this with a stateless backend?

What you do is, yeah, let's store the session in the database.

So you log in, verify, generate session1, but also turn around and store that session1 in the database.

So we have a centralized system where we're storing this thing here, and now we're going to return session1.

The client will refresh, but every time we don't store anything — you may store it if you want, right, locally, it's up to you.

But the backend will always check the database.

"Hey, is this session1 valid?"

Right?

It says, "Hey, session1, yep, it's valid."

If it's valid, go in and say, "yep, that's good, go ahead and continue."

Right.

If it's not valid, then you're going to fail.

Right.

So the session is actually stored persistently somewhere, right?

Whether this is Postgres or Redis, you removed the responsibility here, so you can spin up a number of backends and your app will work.

So if that user profile went to another backend, that works because that backend will still talk to the database, right?

So the entire system is still stateful, because we're storing a state in the database, but the statelessness is a property of the backend application itself.

I can destroy it, I can kill my application, I can restart it and start another one.

Absolutely fine.

And that's the trick here.

And you might, of course, argue with this, saying, "No, it's actually the backend still stateful because it's storing somewhere else."

Right.

It goes back to what do you define stateful and stateless to be, right.

Technically, like in this example, if the database dies, the whole system is pointless.

Right?

Right.

But the backend — even the backend cannot respond to you, because it realizes there is a piece of information that relies on it.

Right.

So, stateful versus stateless protocols.

Now we're talking about the protocols themselves, the way we talk to each other from the client to the server.

Right.

So the protocols can actually be designed to store state, or to not store state, or not rely on any state as well.

That's a very good example here as well.

So for example, TCP is definitely stateful.

Why?

Because there is information stored on both the client and the server.

In both the client and the server, there are sequences.

Every segment that you send is labeled with a sequence, and the sequence is actually stored in the state.

There is literally a state diagram that says, "Oh, the connection is now closed. The connection is now open. The connection is now established. The connection is now waiting."

There is a state machine living here and in the server side.

Right.

And they maintain the connection, they maintain the sequences, they maintain the window sizes, the flow control, the congestion control window.

All of these are state information.

If they are lost, this connection is pointless, it's useless.

You kill the connection right there and then; it's pointless to move on.

You reset the connection if any of these parameters are lost, effectively — that's why TCP is stateful.

Right.

So we have connection, file descriptor, sequences — we talked about.

UDP, on the other hand, is actually completely stateless.

It's message-based, but it doesn't store anything.

Or even if it does store like — there is a file descriptor, but it doesn't really have to be there, right?

You can effectively send multiple datagrams and have these datagrams received by multiple servers, and that's fine.

It's completely stateless.

Let's take DNS for example.

DNS by itself is a stateless protocol.

Why?

Because when you send out a UDP datagram that has the DNS query, right, you don't send it on a connection — it's UDP, it's connectionless, it doesn't have connections.

You simply say, "Hey, I'm going to this IP address."

Right?

And this is the destination port, right?

And you generate a local port, and you generate your, of course, your IP address, and say, "hey, go."

How do you know if we ever responded? In TCP, we have a connection.

Anything coming on this connection — that means I probably sent something and now it's coming in on this connection, you read this connection.

UDP, there is no connection.

So if there are UDP packets, you are responsible to find out what is what.

So that's why DNS actually adds a query ID for every datagram.

So if you send a DNS query, you attach a query ID to it, and the DNS server is responsible once to reply back, "Hey, google.com is actually this IP address. Here is the query ID."

It will actually respond with the query ID back to the port that actually sent it.

Now in this case, the query ID will come back, and the UDP client in this case will read that information, and effectively Linux, the operating system, will map based on the destination port back to the application.

Now you can argue that if the application actually dies in this case, right — if the application actually dies, then there is no one to respond to, to accept this datagram, because the destination port doesn't exist in the operating system.

So you can argue that the DNS client-server is stateful, but the protocol, which is UDP, is stateless.

I don't care, it's your fault that you disconnected.

Right.

Very slippery slope, talking through this.

That's why I don't like talking about this in a very literal way.

I want to just kind of mention it and to learn about it, and then, again, take it with a grain of salt.

These things, these are discussions very hard to discuss.

QUIC, same thing — this would be a little more in a second.

QUIC is very similar — actually, QUIC sends a connection ID to identify the connection, but QUIC is actually stateful because there are all sorts of states, because it acts like TCP, but because it uses UDP, which is stateless, it has to always send the same connection ID with every UDP packet.

So we know that we're talking about this particular connection.

So we're effectively transferring the state across the protocol itself, which is stateless.

And here's another thing that — there is a twist a little bit.

You can build a stateless protocol on top of a stateful one, and vice versa.

Right.

Like HTTP is stateless because it's a request-response.

You can send a request, right, to the server, and then you get back a response.

If the server dies and another server comes back, you don't care, because your next request will have everything it needs to.

It will have this cookie to identify itself.

That's why we need cookies, right?

With HTTP, because cookies will effectively transfer our state, will remember our state, or remember us.

And it is stateless, built on top of TCP.

So what happens if the TCP connection breaks?

Right.

Fine.

HTTP just blindly creates another one.

Who cares?

Right.

It's a medium for transfer.

They don't rely on a TCP connection being always there — if it dies, going to create another one.

That's what we saw with the browser.

And QUIC, for example, is a stateful protocol built on top of UDP.

Right.

Same thing.

So what is a completely stateless system here?

If you think about it, such systems are really rare.

Let's say you — in this case, the state must be carried with every single request, and a backend like — here is an example of a stateless request, where a backend service that relies completely on the input — completely the input itself is — basically, the input itself has everything it needs.

It doesn't really need to talk to another service even.

So the entire system is stateless, if you only rely on the input, for example.

Right.

Let's say I'm building an app to check if a number is a prime or not.

That's a completely stateless system.

Right.

You send a request — I suppose you can store the prime number in a database, and that makes the system kind of stateful.

But in this case, you can just loop through this and say, "hey, this is actually a prime, move on."

JSON Web Token, JWT, is actually a completely stateless system.

Granted, when I — I talked about that on my YouTube channel.

JSON Web Token is not really that secure by itself, because it's stateless, everything is in the token.

It kind of has advantages and disadvantages at the same time.

Because if everything is in this token, then what if someone stole this and you want to mark this as invalid?

This token is bad, someone stole it.

With session IDs, that's easy — go to the database and flip that to invalid.

The next person to actually try to validate, they will query the database and say, "Hey, wait a minute, the session has been revoked. What are you doing?"

But with JWT, you're not talking to another server.

So if the validity of the token was long, right, then whoever stole this token will use it forever, or whatever the validity is.

If I discover it was stolen, someone could have used it to do bad things, right?

It's a problem regardless.

But you have more control over a session versus JWT.

So that's why JWT has the concept of refresh token.

The way you do it is develop a refresh token and the access token, and then make the access token as short as possible.

The refresh token make it longer.

But then the same problem happens, right?

What if someone stole the refresh token?

That's why you have to have TLS in your system, encrypt everything, try to be as delicate as possible, but —

This is, by the way, you guys, don't think that everything is figured out in backend engineering.

I don't think everything is figured out.

I know a lot of engineers talk like they know everything, but not everything is figured out.

There are a lot of holes in current engineering, right.

And the reason is because people will only talk about it when problems happen.

Some people know about these holes, some people don't.

That's why I — when I talk about things, I talk from my current knowledge.

But remember, things can happen, bad things can happen, and there are flaws with everything.

And we try to understand the system.

That's the goal.

And to me, at least, I'm talking to myself — really trying to become a better software engineer, better backend engineer, is just understanding what we have today and the current limitations of this.

And knowing these limitations, you can work around them, and that's the best you can do for now.

Tomorrow something else might happen.

And again, I always add this — definitions go nowhere, so take those with a grain of salt again.

All right guys, that was the session for this stateful versus stateless architecture.

It's mostly that last lecture.
