---
date: 2026-05-20
tags: [backend, communication, http2, multiplexing, connection-pooling]
---

# 14. Multiplexing vs Demultiplexing (h2 proxying vs Connection Pooling)

All right.

Multiplexing versus demultiplexing.

Although this is pure networking speak, it really fits nicely into the discourse when talking about
multiplexing and demultiplexing in popular protocols such as HTTP/2, such as QUIC.

It also shows up in the style of connection pooling, and also in the newer protocol called Multipath
TCP, where we have multiple paths presented to the user as just a single path, a single TCP connection.

It's beautiful to look at this from a different lens, to look at this from the concept of multiplexing
versus demultiplexing.

Let's take a look at that.

So what is multiplexing?

What is demultiplexing?

If you have a lot of requests, a lot of signals coming into a box, right.

And if you shove all these signals into a single line, then this is called multiplexing, effectively
taking multiple lines and then merging them into one.

Think of these as three connections.

For example, three TCP connections.

And this is one TCP connection.

So in this case you're multiplexing.

Think of this as three user events.

Hey, the user sent three requests, request one, two, three.

You're now multiplexing all of them into one connection that goes to the backend, right?

And demultiplexing is the reverse.

You have one thing with three different connections, three different requests.

Now you're demultiplexing them to different clients effectively, right?

So that's demultiplexing.

This is multiplexing.

Why am I talking about this?

What's the criticality here?

Well, it actually shows up everywhere.

So if I have a simple HTTP/1.1 server, right?

HTTP/1.1.

If the user sends three requests, like the actual user working with Chrome in this particular case,
let me disappear for a second here.

If the user sends three requests, request 1 to 3, through fetch or Axios, through the web app,
through XMLHttpRequest, or JavaScript that sends multiple requests.

These requests go to Chrome, and Chrome actually physically opens multiple connections, in
HTTP/1.1, the first version.

And these requests are just pipelined to every connection one by one.

So of course one goes to this connection, one goes to that connection, and so on.

And this goes to the HTTP/1.1 server.

But however, in HTTP/2, right.

We talked about this limitation in the Server-Sent Events lecture, right.

There are only six connections you can open here in Chrome, at least.

Right so.

In HTTP/2.

You only have one connection and you can multiplex these requests into one pipe, one connection, one
channel going to the server.

So you have three streams that are fed to the server multiplexed from the user.

So this is multiplexing, and it shows up also on the backend.

So let's say we have a reverse proxy, let's call it Envoy, for example, or nginx, right?

And then we have the actual HTTP/2 server here on the backend, right?

So the frontend of this proxy is HTTP/1.1 and the backend is HTTP/2, right?

So if I am connected to the reverse proxy directly, then I establish three TCP connections, for example.

Right?

And I send all of them in parallel.

The proxy.

In this case it will establish one TCP, one HTTP/2 connection, which is a TCP connection, and then
multiplex this into the backend.

Right.

The beauty of this is you kind of have one connection to the backend.

You have fewer number of connections.

But the limitation of this, at least in HTTP/2, when we're going to talk about HTTP/2, we're
going to see this limitation.

I found out that this actually puts some load on the HTTP/2 server because the CPU needs to
stop parsing these requests from different streams on the same connection and that takes time.

So you get more throughput but at the cost of more resources here.

So let's talk about demultiplexing.

So if you actually flip this, right.

In demultiplexing, if you flip this, you have an HTTP/2 connection from the client
to the frontend of the proxy, the reverse proxy.

And now all of these are multiplexed, as we talked about, but now the proxy gets that and then it demultiplexes
them to the server.

Each request goes into its own connection.

So I wanted to show you this diagram so that when they show up in your work or your engineering
tasks, you are aware of these multiplexing and demultiplexing patterns because there are advantages and disadvantages of multiplexing.

It's really good to have each request go into its own connection because it will have its own rules
and flow control and congestion control.

Each connection has different rules.

So they are not affecting each other compared to this where it sits on the same TCP connection and
it abides by the rules of that single connection.

That's no longer true in QUIC though, and.

Connection pooling.

One of my favorite things and very, very popular.

You know, connection pooling is a technique where you can effectively spin up multiple
database connections, or it doesn't have to be a database.

Really?

Right.

If you have a database server here and you have a backend server, a web server, and we need to execute
some queries to the Postgres server, you would establish X amount of connections, keep them
hot.

And when a request comes in from the client, we will pick a free connection.

Right.

You have four in this case.

And when we send the request, the yellow request takes the next connection, the
orange, the pinkish request here goes to the third connection.

So we're going to have a pool of connections instead of establishing a connection on every request,
and instead of having one connection competing.

Right.

With many requests, we're going to open a pool of connections, right?

Django effectively doesn't do this.

It does.

I don't know if you're familiar with Django, the Python library, it uses just a single connection
per thread, which kind of is very limited in this case.

Right?

You're basically at the mercy of the threading architecture in your backend, right.

But you can effectively set up another backend with multiple connections and have them this way.

But now if I have this purple request, a web request to do a `/comments`.

Right.

Hey, these are busy now.

Let's say we didn't get a response yet.

Hey, there is a free connection.

Use it to send the SQL query on this database connection.

But then another red connection.

Now we have four of them are busy, and let's say the max is four for connection pooling.

Here's what happened, which is that we maxed it out.

And you can see that this is effectively demultiplexing, right?

The Connection Pool is just demultiplexing.

It's a glorified demultiplexing style, right? Where we have kind of one pipe coming here,
maybe one connection, but we are demultiplexing the requests on the backend.

Right.

So now the request will actually be blocked, right?

Yeah.

It arrived at the backend, but it cannot be served because all of these connections are busy.

So that's basically what connection pooling is, very popular.

Consider using it in your style of programming.

But now let's say this request is done, the request is done.

We got a response.

We responded back to the client.

Now one connection has opened up and we can send that request, right, the SQL query on that
empty, idle connection.

Let's say you might say, Hussein, why can't I send multiple SQL queries on the same connection?

Why do I have to wait for that response?

That's a whole different discussion, to be honest.

If you send.

The.

Let's talk about it.

Sure, why not?

If you send three SQL queries on the same connection.

Right.

And the Postgres server receives SQL.

One, two, three.

Let's assume they are received in order.

SQL one, two, three will get executed.

Right.

Let's say SQL one took 7 seconds, SQL two took two seconds, and SQL three finished first.

Right?

Took a second.

So SQL three will respond first because we're not going to wait — it is going to respond to you with SQL three,
the SQL three response.

How does the backend know that the response we just got is for SQL three and not for SQL one?

Right?

You have no idea.

The order is not guaranteed.

You're talking to a black box, Postgres.

You are at the mercy of how Postgres processes queries.

You cannot just send three requests and say, hey, whatever is coming, I'll consume it.

You can play with tricks where you can tag the SQL with a certain ID and make sure the IDs
come back.

You can build that as your backend, but without that right, you can't effectively do it.

Pipelining has actually been recently supported.

In Postgres 14, if I'm not mistaken, they started supporting the idea of pipelining, which is this idea of sending multiple
queries on the same connection and having them be responded to in order effectively.

So let's talk about browser demultiplexing in HTTP/1.1.

Right.

So this is kind of — we talked about it, we talked about this limitation actually in HTTP/1.1 where we
have only six connections, right?

So we already sent one request, request one, request two, request three. These connections are busy, right?

Remember the Server-Sent Events case is a reverse case here, and if you send more requests you only have
three connections.

So if you send another three requests these will be busy.

Right.

And if you send more and more requests, you effectively don't have any resources left and your requests
will be blocked in this case.

Right.

How about we actually do a demo showing how the browser connection pool in HTTP/1.1 can effectively
block the requests.

All right.

So I'm going to use this the same long polling example that we did.

And what we're going to do is go to the browser and submit a job.

Let's go ahead and submit a job in the browser.

And from that job, what we're going to do is we're going to continue checking the status.

So we're going to send multiple connections to the server with this, right?

So we've got a job.

What we're going to do is we're going to make a query here and then the job ID, I'm going to check
for this job.

And because it's long polling, remember, the request will be used, right.

And that connection will be occupied.

So we're going to send a bunch of requests.

And of course, we're not going to get a response because this is long polling.

I'm going to send a bunch of them right here and.

Not as now.

If I try to submit, I'm blocked.

I can't even submit jobs because now the connection pool is actually filled.

All of these are busy and now you cannot send any more requests until some of these are actually done.

So if you continue watching this for a minute, you can see that these are my submitted requests.

Some of them succeeded, some of them are pending.

Remember, the submit job is an immediate thing.

I should immediately get a response.

But now we're blocked.

Nobody's actually unblocking the client side.

No one else can send.

And immediately look at this.

The moment we get responses here, we unblock the other requests and now we transmit them to the server.

And now you can see the waterfall for requests to see the blocking that happens.

And these are all connections that have been effectively stalled, right?

They've been stalled for 1.2 minutes and stalling happens usually on the client side.

And we didn't even attempt to establish a new connection because we couldn't, we didn't
have enough resources.

And now we can see that one, two, three, four, five, six.

This green line is the six connections, and that's it.

You cannot effectively establish more connections in the browser.

Why?

Because the protocol is HTTP/1.1.

The client by default submits six connections and even the six connections are really controlled by Chrome.

Sometimes it just forces you to wait.

It doesn't establish another one unless it absolutely needs to.

So you can get into this.

And it's very clear from here.

Right.

And the connections, you can see that if I expand and look at the connection IDs.

Or if you count them, they're going to be six, right?

5353. All of them are the same connection.

Same connection.

One connection.

11 is another connection.

So we have 271, 364.

Well.

46553.

It's the same one, right.

49.

I suppose 49 is the new one.

Right.

47.

We didn't see 47 before.

Right.

92.

And you might see the same connections.

It doesn't have to be six because some connections might have been closed and reopened again.

That's why reusability is really important here for Chrome to not exceed that hard number
of six connections.

And again this is a long-standing issue and this is all per-domain.

Of course, if you have multiple domains, you can open multiple connections.

All right.

That ends the multiplexing and demultiplexing lecture. See you in the next one.
