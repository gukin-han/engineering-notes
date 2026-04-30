---
date: 2026-04-30
tags: [backend, networking, load-balancer]
---

# 52. Layer 4 vs Layer 7 Load Balancers

We talked about the difference between a proxy and a reverse proxy when it comes to the networking

aspect of this.

What is exactly happening when you use a proxy?

What connections are being made and who's talking to whom effectively?

This is very critical to understand as a back end engineer, especially if you're using this on a daily

basis.

But then another important concept is also the layer four and the layer seven proxies or reverse proxies

or load balancers.

What I did, I built up this lecture specifically to differentiate the differences between Layer

four load balancers and layer seven load balancers.

And this is specifically a back end concept, if you will, but it's tight and in with networking.

So I couldn't really explain this lecture if one did not understand the basic principle of networking,

which you should by now the end of the course.

How about we jump into this fantastic course was one of the my favorite.

Let's jump into it.

Layer four versus layer seven load balancers.

And you can replace load balancers with reverse proxies and it will be identical effectively.

And load balancer is a reverse proxy is just one case of a reverse proxy that happens to have logic

to balance the request between multiple backends on the back end, while a reverse proxy is just doesn't

have to balance, it makes requests on your behalf.

It just talks to a back end.

But it doesn't have balancing logic necessarily.

Every load balancer is a reverse proxy, but not every reverse proxy is a load balancer.

And proxy has nothing to do with any of this stuff.

The concept of just a proxy, well, that out of the way, the fundamental component of back in networking

that layer four and layer seven load balancer we've been talking about back in June understanding these

two pieces very critically.

This is the agenda of this particular lecture we're going to talk about What is the difference between

layer four, Layer seven?

We already know, but it doesn't hurt to mention it.

We're going to talk about what is a load balancer, going to mention the load for layer four, load

balancer, the pros and cons, going to talk about the pros and cons of this layer for load balancing.

And we're going to talk about layer seven load balancing effectively and also going to talk about the

pros and cons.

Let's jump into it.

This is our beautiful OSI model, the physical, the data link, the networking, the transport layer,

the session presentation application.

What we're really interested in as backend engineers is layer seven and layer four.

We most of the time we play this layer, some of us play this layer, we want it to match manage file

descriptors, play with sessions, see how many segments this connection has received, see how many

bytes this connection had, stuff like that.

This information is stored stateful in layer five because it's the session layer.

But that's pretty much what we always play in these two layers.

So what does that mean when it comes to actual load balancing?

Here is what a load balancer is, is also known as a fault tolerant system.

I want to build a system that is fault tolerant such that it will make a request as a client

to this load balancer.

It it can talk to one backend or several and I don't really care how many backends.

That's the beauty of the reverse proxy.

The reverse proxy.

You talk to this back end and that said, it takes care of talking to many, many backends on the backend.

So a layer for load balancer, if you put in some IP addresses here is it starts up and it establishes

some TCP connections on the backend, that's when it starts up, just warms up, it opens multiple

connections.

Doesn't have to be one could be ten per back end and then just keeps them warm.

This is the idea of it's called warming up.

The TCP connection, it's just keeping things hot so that we don't have to suffer from SYN.

And ACK ACK on requests.

We have the connections warm and just we can send our segments.

So now if a user establishes a connection to my load layer for load balancer, what will happen here

is that connection will have a state in the load layer for load balancer and it will tag it to one.

One and only one connection to the backend.

And that's the contract here, because a layer four only deals with ports, IP addresses, and the data

is just segments that I should really touch or try to parse.

That means when the client connects to the layer for load balancer, the LB chooses one server and all

segments that the client sends to me.

All of them has to go to one connection, not only just one server, one connection.

And as long as this you're sending me data on this connection, you have to always send me to this.

Because it's a stateful thing.

A layer four is stateful.

We talked about this.

So if you send me data, I can't just send one segment here to one segment here.

The sequences will be out of sync and then everything will be bad.

The data will be corrupted.

You can just do that.

So if you send me a segment on here, take the data and then rewrite it onto that connection.

So these are two different connections if you think about it.

And when you write a segment here, you rewrite it effectively on the back and on that connection.

So it's the different sequences.

Everything is different here.

And so that's one mode or layer forward or advanced, or at least there is another mode that it's called

the NAT mode, which really makes everything into a single TCP connection.

And we talked about that in previous lectures as well.

But we're interested in the common cases.

In the Nat case, the client is the load balancer is the gateway of the client and any connection

you make, the load balancer effectively acts like almost like a router.

In this case and just changes the destination IP address to the back end.

Let's take some examples here.

Now what happens if I send an IP packet here?

I'm going to test it to 44.1.2.

That's the load balancer.

This is a reverse proxy.

That's my final destination.

I send the packet and the data is taken here and then literally rewritten into a brand new TCP connection

on the backend.

So for 4.1 to 3, in this case, this is my destination and the source becomes me as the load balancer.

And the client is completely unaware of that when the when the, when the backend receives that segment

delivered to the app, maybe it goes to a TCP app or socket, gRPC, anything.

What happens here is the backend responds back, but now the destination becomes the load balancer,

the source becomes this, that the load balancer takes this packet.

It knows that anything I receive from this connection as a layer for load balancer, I have to send

it back to this particular connection.

It knows there is a table that keeps and it keeps this mapping, so it has to respond back to the actual

client and it puts the destination as the client and the response content and then sends it up.

And this becomes a sticky thing all the time.

As long as the connection you're sending the same connection, it's always going to the same back end.

Let's take an example where we're sending an actual HTTP request here, I'm going to send an HTTP

get request here.

Number one.

I'm sending slash one.

I'm getting some API here.

And let's say that these are maybe multiple segments, so I'm going to send two segments here.

So segment one and segment two, the load for layer four balancer receives those segments and then maybe

chooses to acknowledge them.

But once it's acknowledge it, it just forward those segments directly to the to the back end that it

chooses.

For that connection.

And that's it.

It moves on.

So if the client sends another segment, it just writes it back.

So all of this goes to the same connection all the time as I receive segment.

I don't look what's the destination?

Oh, you want me to write this?

Read, write, read, write.

I don't read.

I don't try to do anything.

There is no buffering.

There's nothing I read and write instead of the situation.

Maybe some smart layer followed by There might need to do some buffering to take advantage of larger

MTU.

Maybe here.

Like if the MTU is here on one 1500 and then to use zero 9000, maybe it's advantageous for the layer

for load balancer to read, read, read, read and then write smaller segments.

So that's one case that you can do that, but that's back to the performance, baby.

We we always really try to squeeze as much performance as possible and try to challenge everything.

All right, so let's send another request, Stevie, to on the same connection.

Doesn't matter.

Five, six, five, six.

Goes always to the same back end.

As long as you're sending me to the same connection.

And the second segment seven goes there.

So it's just as you say.

Immediately get it there.

So now what happened?

If I open a new collection and send requests, get three in this case?

Since it's a new connection, the load balancing logic will be triggers at.

Oh, and you connection.

I have to choose a backend.

The second backend will be chosen based on the load balancing and I'll go to them.

It could be a round robin, could be least, at least connected.

Who?

Whoever is least overwhelmed will be picked and the connections will be established.

So in this case I picked this.

Maybe I receive one segment.

That segment I see the second segment.

This segment third.

It so did it.

I don't even know what's in this.

This guy does not know it's HTTP, it knows it's TCP, it's segment and that's it.

It does not know which TLS, it doesn't know it's encrypted.

It doesn't know anything.

If this was a protocol buffers, it was gRPC, if it was WebSockets, if it was MySQL connection.

Postgres it does not matter because I treat this as pure TCP connection and that's it.

It's just segments to me.

And that's the beauty of Layer four.

Let me answer this lack of knowledge, this naivety.

It's critical and beautiful because it makes mixed layer for load balancer supports really any protocol

you want because hey, it's just data, it's just segments.

So what's the pros and cons of this?

The pros.

Let's talk about the pros.

It's a simple load balancing because it's literally doesn't do with the data.

It doesn't really read the protocol, doesn't really understand what layer seven content is this?

It took a layer four.

So a port IP addresses, that's it, and connections.

And so it's really efficient.

There is no data lockup.

It doesn't look at any data.

It just look that bought the IP addresses and sometimes it buffers and sometimes a bit depends on them

to you.

But most of the time just takes the segment writers to the back end.

It's more secure because we're going to talk about this layer seven load balancing actually needs to

read the content and in order to read to cache or to do pass or API gateway, it needs to decrypt

the content.

Yeah, it has to decrypt the content to load balance that some people might not be comfortable with that

if if the load balancer is a third party.

But layer four don't have to decrypt because it's almost end to end in this case.

Not almost, of course.

So it works with any protocol.

That's beautiful.

Because it because it doesn't look at the content.

It's agnostic.

Say, hey, you send me segments.

I'm saying I'm sending it back to the server.

It can work with one TCP connection in a NAT configuration where if if, if your client immediately

connects to the load balancer as its gateway, which is not very common.

A It will the whole thing will be in one TCP connection to the back end immediately.

All what the router will do, or in this case load balancer is just rewrite the destination IP address

to be the back end.

Just change the segment.

Write the segment IP address.

In this case the IP packet, IP address the destination to be the destination IP address or maybe the

port in this case as well to change the port.

So it becomes really using pure nat.

And we talked about this in the NAT configuration.

Cons here's about the bad thing about this.

There is no smart load balancing because you are not looking at the data.

You can make smart decisions.

Maybe let's say you are sending specific requests that are we know there is this is heavy consuming

workload.

I know this particular request.

If it goes to this path slash, I don't know, load or slash analyze as an API.

This is consuming.

I want it to to move this to a specific backends are beefy.

You can do this with layer four load better because you don't know anything.

You can play tricks, you can configure the layer for load balancer to have multiple IP addresses,

not multiple story ports.

And if you're connected to this port, that means you want this particular backend.

If you connect to this port, this partner back.

And so we play with ports because port is visible for the layer four, you can definitely do that and

it's not applicable for microservices.

We talked about this.

Yeah, we need this smarter logic, although you can use the idea of ports and IP addresses

to do that.

A sticky connection.

We talked about this, so all the segments that you send on the connection will always go to one server

and only one connection.

There is no load balancing per connection.

And then the reason is because I don't know what these segments mean.

That is just if you send a request or you send a post request or you send three get requests, layer

for load balancer doesn't know anything.

It sees this as a bunch of segments coming in.

So as a result, just has to deliver them to the backend.

And one backend because it can mean assumption.

Layer seven smart enough it knows so it can pick and choose what segments is in.

No caching.

Because.

Because there is no because I don't know what's in there.

I cannot cache it.

You might say, okay, if I send the same segment over and over again, the load balancer can crush

it.

Not really, because what does that segment mean?

Yeah, it's the same value.

But what does this represent?

I have no idea.

You can send something that hashes to a certain value, but it could mean something completely different.

It's a protocol anywhere.

So while this was an advantage, there's also a double edged sword.

It's also a disadvantage because it can be dangerous.

There is one case where layer four, layer seven.

Load balancer can be downgraded to a layer for load balancer.

If you sent an upgrade protocol initiative, there is an upgrade method that upgrades the connection

to either WebSockets or HTTP/2 to over clear text.

And if you do that because WebSockets is is really a special protocol, the easiest way to do is from

moving from layer seven.

You can just move to layer four to support any protocol.

So if I want to support WebSockets, just move it to a layer four protocol and then treat it as a

layer four.

And as a result, the moment you treat a layer for any logic that you might have in the load balancer

tool, I say, okay, I want to block certain users, I want to block certain authentication methods,

I want to block certain headers.

You can't do any of that anymore.

Because you used to have these rules when you were a layer seven load balancer, but when you were downgraded

to a layer four, everything was allowed were allowed.

And that's the problem.

So that's one cones of layer four, layer seven load balancer, very similar.

When you spin up a layer seven load balancer, it just it uses the same concept, the TCP connection

to the back ends, it just warms them up and opens up as many as it can or as configured and that moves

on.

So now when a client actually connects.

To a back end, right to the layer seven back in here.

What happens here is any it becomes a protocol specific.

It means, you're connected to me, but you get to tell me what you are.

What are you sending me here?

You can't just send me garbage.

It needs to understand everything you're sending.

So any logical request that you send logical request.

We're going to talk.

About what mean?

What do you mean by a logical request?

A logical request here will be buffered and will load.

So try to understand it and only then it will pass it and then make the decision to forward it to the

vacuum.

So a request here at HTTP request, let's take an example is the start of HTTP slash.

Slash.

One one slash the path, blah, blah, blah.

And then at the end is what the the version believe there should be one one slash.

Yeah.

That should be version.

And then that becomes becomes the http and then the headers follows.

Then at the end of the day, we add a bunch of new lines.

That's the end of the request.

That is when the load is All right, that's a request.

Stop.

Let me choose which back into the write this request to.

So that request could be one segment, could be true, could be 100 segments.

So it needs to read.

Reid, Reid, Reid, Reid, Reid, Reid and buffer and.

Read the data and if it's encrypted, it needs to decrypt the data.

What does that mean?

It means that to decrypt the data, you have to have a connection between you and the server and also

a secure connection between you and the server.

That means if you ever want to host your website, the certificate has to live in the Layer seven load

balancer.

A lot of people just don't like to do that because your private key has to live in the layer seven.

Otherwise, how how can you pretend to be Google or your website?

In this case or otherwise?

How can you pretend to be your website this high?

This guy has to pretend to be your website.

Because that's the final destination to your client.

So you have to give it the certificate.

So that's the decrypt has to read and then as a result, understand.

So let's take an example.

Same thing.

We send our IP back, it goes to a back end and then we send back a response and it goes.

This same thing here doesn't change, but let's say I'm sending a bigger request here slash one and

this is a bunch of a bunch of segments.

So segment one is still part of the Get one, Segment two and Segment three.

All of these combined will become the HTTP get one slash request.

So the LB does just try to understand it buffers this data to understand the segments and then says,

okay, one, two, three up.

There you go.

There's a new line.

Here's one request.

Now let me take these guys and then write them one by one to the actual back end.

So all these segments become one unit, one request unit, and all of them has to go to one back end.

That's it.

So although you send me a request on this connection, three segments goes to this server.

But let's take an example.

I'm sending another request on the same connection, five, six and seven segments that that's fine.

That's a completely distinct request.

I read it.

I understood where you want it to be is stateless so I can pick another server and send five, six,

seven, two.

So I will send I will write the segments y67 that represent the request and this guy is will need to

parse that HTTP request in this case and understand it.

This is, by the way, where most of the HTTP smuggling attacks happen.

If you write about http smuggling is an attack where where if those guys do not agree.

If the load balancer and the backend doesn't do not agree on where the request starts and where the

request end, bad things happen.

This is outside of the realm of this.

You can read more about this if you want, outside the scope of this lecture.

Otherwise this lecture would be 3 hours.

But yet I can establish a new connection.

And the same thing we can.

That connection will use that same connection.

This is something we can do, by the way, in layer seven load balancer.

We don't talk about it once in layer four, once someone connects and.

Reserves a connection.

That back end connection cannot be used for any other clients.

That's, I guess, another disadvantage of the layer four load balancer becomes a private.

They call it private connection sometimes.

So that's a big disadvantage in layer four.

Because you can deplete your back end connections while layer seven load balancer are actually nicely

balanced through the connections.

That's powerful stuff right there.

Pros.

So as there is a smart load balancing, there is actual load balancing happening here.

We are efficient in using the connections on the back end.

We can cache because now I'm reading, I'm decrypting, I'm understanding, I can cache here, I can

do a smart load balancing.

Hey, if you're going to slash pictures, I can take you to this server.

If you're going to slash comments, I can take you to this server.

If you're going to slash post comment, this is a right heavy workload.

Go to this server because it has a specific database designed for this particular workload.

If you want to analyze or do an overlap, here here is a a server that points to a database

that is all app specific, such as SAP, HANA or Postgres, MariaDB.

So you can a column base storage, you can do all sorts of smart things.

So that's why it's great for microservices API gateway logic.

Like we explain you can API, gateway authentication can happen, everything can happen in the

load balancer.

As a result, you can cache results.

What's the cons?

It's expensive.

Why?

Because it's doing more work.

It's buffering, it's reading, it's decrypting.

So it's more, it's more expensive.

It's decrypting the content.

So it terminates the TLS.

That's why it's called TLS Terminator.

Load balancer is always called TLS Terminator and layer seven.

When we whenever we say layer seven, it has to terminate connection it uses to TCP connection.

I don't know if this is the cons really, I don't think so.

But just there is a way that layer four can use one connection.

So that's why I put it there.

Is just a disadvantage.

But you can you can add that to the prose section here where it's a very efficient use of connection

in layer seven.

It must share that the other cert, that's a cons as well, because some people don't like that

it needs to buffer.

So there's a performance results.

So you need to read, read the request, understand that it goes all the way then so that while the

actual back end is waiting meanwhile.

So if there is like.

The load balancer becomes a bottleneck if it's buffering a lot of requests it's doing at the same time,

it can slow down.

And the problem here, this is the major one.

It needs to understand the protocol why there or there is requests going all the time, people

actually posting features from HAProxy and nginx say, hey, please support WebSocket.

Oh guys, can you support gRPC?

Oh, guys, can you support I don't know, with this protocol.

Can you support Postgres?

Why?

Because of Because we need to do layer seven load balancing.

You cannot do layer seven load balancing if the load balancer doesn't understand the protocol.

Because we are.

Parsing the data we're reading.

And as a result, if we don't understand how to talk to the back end and receive data, we're done.

That's why layers have a lot.

They need to understand the protocols.

Layer for bands in this case can be used if you if you don't if your protocol cannot be interpreted.

Summary we talked about layer four versus layer seven.

We talked about what is the load balancer.

Just a glorified reverse proxy that's a smart enough to do actual load balancing.

We talk about layer for load balancing pros and cons.

We talked about layer seven load balancing and the pros and cons.

There is no right or wrong.

There is a case for every load balancer.

Guys, hope you enjoy this lecture.

Going to see you on the next one.

Enjoy the course.

