---
date: 2026-04-30
tags: [backend, networking, websocket, proxy]
---

# 53. WebSocket Proxying

All right, let's get to the meat of this work or this course.

And the idea here we're going to discuss in this section is we're going to discuss layer four and layer

seven WebSockets proxying.

And we're going to take a few slides talking about layer four versus layer seven.

And then we're going to add websockets to it.

And what's the difference between the two.

Let's get into it.

So in the layer four OSI model we see the TCP IP content.

So what does that mean.

The TCP stands for Transmission Control Protocol.

The IP stands for the Internet Protocol.

Let's translate this to engineering speak.

The software engineering aspect of this.

The network engineer looks at this completely differently to US backend and software engineers.

Here's how I see this.

The IP.

Think of this as a packet that has As everything at the IP stack, which is layer three, has the destination,

which is the destination IP address, the source IP address and some sort of data, and the TCP stack,

which is higher up using the IP, adds two additional useful information, plus so much other stuff.

Additionally, it adds the ports, the source port, the destination port, and then shoves it in an

IP packet.

So we call it TCP segments, an IP packet.

So what do we see in the TCP IP.

We see four things.

We see ports IP addresses and the idea of connections in the TCP.

Because the TCP establishes this idea of SYN and ACK and sequences.

And it's a very stateful protocol.

So in a layer four concept, we really shouldn't look at the data inside the IP or the TCP.

We should only look at this metadata which is IP addresses ports.

And if you want the connections and sequences, stuff like that.

But we really shouldn't look at the data.

And even if we tried, the data might be just encrypted because we did a TLS handshake before and the

content is actually encrypted, you really cannot see it.

And even if it is unencrypted, let's say it's port 80.

And you're sending some data.

We really should not.

And then I put should not on double code.

People still do.

Routers still do these things.

But that's a general rule of thumb.

We don't really look at the content as per the rules.

Yeah.

Otherwise if you start looking at that then you are classified as another kind of layer.

Because you're digging into things that are not your concern.

However, in a layer seven,

I skipped layer six and five because they are irrelevant in this discussion.

Layer seven is the application.

Layer four is the transport layer.

Look at this.

This way in layer seven this is the application.

So you have access to everything that you see in layer four plus application layer content.

You can see the content.

Guess what.

You can decrypt the content.

By definition if you're a layer seven.

Application you have to decrypt the content.

You have to terminate TLS.

You have to serve your own certificate.

You have to look completely at the content.

It is unlike layer four where hey, I look at this.

If there is by default, default for me, which is the port, the IP addresses, stuff like that.

These are not encrypted and will never be encrypted.

So the beauty of this is in layer seven you see the content.

That means you see more useful information, not just ports and IP addresses.

You have context of the application.

You have access to the headers, HTTP headers.

You have access to the past, like oh, we're going to slash chat, we're going

to slash blah blah blah blah blah blah slash.

JPEG.

And layer four.

You can't do that because you can't look at the data in layer seven.

You see all this stuff so you can do clever things.

Hey, if someone's going to slash chat to come to this server, if they're going to slash media, oh,

media is heavier than chat.

Take him to this server.

This is a beefier server.

So you can do these kind of smart rules in nginx and any proxy.

So that's the beauty of this.

So now we understand layer four and layer seven.

Now that we know layer four and layer seven layers and which what do we see in each.

Now we go back to the WebSockets in layer four proxy.

When it comes to WebSocket and I say proxying, I mean reverse proxying to be specific and anything

related to load balancing as a subset.

If you wanted to or API gateway and stuff like that.

Reverse proxy that's what I mean.

But it's just simple to say proxy.

You get the idea.

So layer four proxy on WebSocket is done as a simple tunnel.

The moment you send me a TCP request and I know as a proxy as nginx, I know that I'm supposed

to just take your word for it and pick a backend, and I'm going to tunnel everything that you send

me always to that back end.

So nginx will intercept the SYN request for a connection and create another connection on the back

end.

Again, this is one implementation.

I'm not sure exactly how nginx does it, but one implementation might be this.

Some implementations are smarter than that.

Try to be, because stateful proxying like that, reserving a connection always

to the back end can be expensive.

So nginx some proxies takes shortcuts.

It tries to optimize these kind of things.

But this is the general idea.

If the client wants to connect to nginx to this port, and we know this port is a layer

for proxying front end port, it will always create a back end and then anything in the future,

any data sent to this front end connection is tunneled to the back end connection.

It's just blindly.

So whether it's HTTP, whether it's gRPC, whether it's anything WebSockets I don't care.

I'm going to tunnel you always to the same backend.

And just like that, if your back end does support WebSockets.

Nginx doesn't even need to understand the WebSocket protocol.

Nginx in this configuration doesn't need to understand any protocol because it's a dumb tunnel.

So the backend connection remains private and dedicated to this client.

The second example layer for proxying on web sockets.

So we have a client here.

And now we added nginx in the middle as a layer four proxy.

And then we added the server which is a WebSocket server listening on port 443 for this particular example.

And I made this secure to show you exactly what's happening here in this time.

The first thing we're going to do is the client.

We're going to open a connection.

And then the moment the request of the connection opening goes to nginx on that port, nginx knows,

oh, this is a layer four, I'm going to tunnel.

It's going to open a back end connection.

And then here's what happened.

The client will send a TLS handshake.

What would the nginx do?

Well, the nginx is in this configuration.

It's a layer four.

So that means anything you send me, I'm just going to send it back all the way to the back

end.

So the TLS handshake, which is the request to encrypt, goes all the way to the back end.

And the back end will just receive.

Oh, you want to encrypt.

The back end will respond to nginx.

Sure.

And it says okay, here's my information.

Here's my certificate.

Here's my part of the Diffie-Hellman encryption keys.

And then nginx.

What does it do.

Say hey, are you responding to this tunnel?

This tunnel belongs to this client.

Boom.

I'm just responding back.

And here's what will happen.

Immediately the client gets a key, and the server gets the same key.

That's the purpose of a handshake.

The goal is to establish a symmetric key that we can use to encrypt the communication.

And guess what?

Engineers have no idea about this key.

Engineers absolutely cannot get this key unless it did something shady, which we're not going to

talk about.

But engineers in this configuration cannot get this key.

So this is a complete end to end encryption.

Because my middle boxes cannot read my data.

So now when I send data it says okay upgrade request.

This is a request to upgrade the WebSocket connection.

I just stripped it out of all the complexity for simplicity.

Well what do we do?

Well this is nginx.

Hey nginx this to nginx.

It doesn't even know it's an upgrade request.

It receives a request on this port.

Hey.

It's encrypted.

I don't even know if it's encrypted because I'm not supposed to look.

And even if I look, I cannot decrypt anything.

So it says, oh, you're going to this port.

You blindly follow it to the backend.

The backend understands WebSocket protocol.

So we're going to play with switching protocol.

Then what does it do.

Just forward the packets all the way to the client.

And that's it.

And now anything that you send already it's a tunnel.

So anything the client will send, the nginx will just blindly

send it to the backend.

And the backend will respond with the response.

And then the nginx will just forward that response to the client.

And that's it.

And this is now a bidirectional communication.

That's fine because we know and here's what's very important here.

The important thing here is you have to configure the timeouts so that nginx doesn't decide

to close the connection just because there is no enough data or nobody sent anything for a while.

This is where it gets really tricky and you have to work the configuration a little bit, and then once the client closes the connection,

the server can safely close that private back end connection.

Let's take a look at layer seven proxy on WebSockets.

So layer seven proxying.

Let's do the same exact scenario and see what's the difference here.

Now we're going to open the connection.

See nginx didn't open a connection.

Okay.

Sure I'm a layer seven I'm advance.

What you're sending me.

You're sending me a TLS handshake okay.

You're sending me a TLS handshake.

Here's the thing.

You're sending a TLS handshake.

I'm a layer seven proxy.

I got response to you.

It's going to respond back to the client with nginx certificate.

With nginx public key.

With nginx Diffie-Hellman parameters, you are establishing a TLS session between you and nginx.

Nginx wants to decrypt anything between you and the whatever server you want to connect to, but

it needs to see whatever you see.

Every API gateway does exactly that in order to see what you're sending.

And not a lot of people know that, unfortunately.

And that's why I want to clarify this picture even more.

That's why in this configuration you have to put certificates here.

You have to put the private key.

Sometimes you have to even share the certificate and the private key of the server in nginx.

And this is frowned upon a lot.

Not a lot of people like that, A lot of people do like to share the same certificate.

Some people like to create a unique certificate, unique SSL keys for nginx.

All right.

And nginx have their own keys.

Nginx.

And the client has the same key.

And now the client says, okay, I want to upgrade my WebSocket connection.

Here's all the information.

Nginx sees that in full.

Remember that nginx even didn't touch a backend yet.

This nginx is like.

All right, I gotta see for myself first.

What are you trying to do?

I'm gonna.

I'm gonna connect to a backend.

And this is not just one backend.

It could be 7 or 8.

Any number on the backend.

And nginx will load balance accordingly.

So now when I do an upgrade request, nginx will say okay all right upgrade request.

Let me see I am configured on this port to load, balance and proxy on this particular server.

Let me open a connection and let me send a TLS handshake because this is secure.

We have another secure session between nginx and the back end.

And if the backend doesn't support TLS, it will be unencrypted.

Although this is the preferred configuration, especially in the cloud where even things that

you're sending this on the back end and if it's a private LAN, who cares?

Nobody have access to it.

But if it's in the cloud, I would still not trust an unencrypted connection because

you never know who your data is shared with the cloud.

Really?

They're using all this software defined networking and whatnot.

Everything is shared.

It depends on how sensitive the data at the end of the day.

but.

Yeah.

All right.

The cloud provider could, if they want, they could sniff your traffic

and you might not want that anyway when you encrypt the back end.

Now nginx will send this upgrade request a brand new upgrade request to it.

And now the server will respond okay.

Switching protocol to nginx.

And now only when nginx receives that switching protocol, nginx will say okay, it will issue its own

switching protocol.

That's a completely different from this to this because this connection is different than this.

And here's what you need to understand.

So now if you're going to slash chat or if you're going to slash super chat or if you're going to slash

feed, nginx can take you exactly to the server that you want to connect to, and it can do smart proxy

if you're going to slash index dot HTML.

Well, this is just a normal HTML.

Let me fetch that page.

If you're going to slash chat oh this is a WebSocket request.

Let me go there so it can do smart things like that.

Layer four cannot do any of this fancy stuff.

Now this is a WebSocket connection and this is a WebSocket connection.

In the previous configuration with layer four the whole thing is just one tunnel.

It's end to end.

Now we have two WebSockets connection technically and they are tagged to each other.

Anything you send here goes to this connection.

Always.

Anything goes in here goes to this connection and it's decrypted.

It's always encrypted.

Let's go again.

Let's do it again.

So we do it again.

Client wants to send a WebSocket message.

It encrypts it with the pink key.

Sends it to the nginx.

Nginx takes it, decrypts it with the pinky.

Take a look at the content.

Maybe does stuff with it.

Let's say you want to ban bad words.

You can implement that logic in nginx you want.

Hey, I don't want someone say this word, for example.

Well, you can look at the content here.

Decrypt it.

Ban these kind of words.

Very easy.

Now nginx.

Once it passes this kind of messaging it takes that message, encrypts it with the golden key, sends

it to the back end.

The back end takes it, decrypts it with the golden key.

Its own golden key, which only these guys have.

And then does its own thing and then send the respond back.

And then the same thing.

It's encrypted with the golden key and the nginx decrypts it.

And once it gets all these messages, then the nginx encrypts it back with a pink key,

send it back all the way to the client.

And you get the idea.

Once the client can close, the back end is also closed.

Pretty neat stuff, huh?

Alright, now that we know what happens in the wire.

With WebSockets and the tunneling and layer four and layer seven, let's zoom out a little bit and see

what these normal requests look like in a normal HTTP request in a layer seven load balancing.

So what's the difference between load balancing and proxying.

Exactly identical.

The same thing.

Load balancing is just a smarter reverse proxy.

Load balancer is a reverse proxy.

Every load balancer is a reverse proxy.

But not every reverse proxy is a load balancer.

Does that make sense?

Because reverse proxy just terminates the connection and then sends it to the backend.

A load balancer terminates the connection.

Send it to the backend in a smart way.

Says okay, I'm going to send it to this backend.

I'm going to send it to the backend.

The idea is just what do you do with the load balancer?

Now what happens here is nginx preheats the back end connections.

Okay.

This is an HTTP layer seven load balancer.

All right.

Let me just open a bunch of connections on the back end.

Let's warm them up.

Again not every proxy does that.

Sometimes nginx does it.

Sometimes it does not.

It depends on the situation.

It depends on the memory.

It depends on so many other things.

It depends on even in the configuration.

Some configurations say, hey, I want you to preheat this many connections.

Leave this many connections open.

So nginx preheats a bunch of TCP connections on the back end ready for use.

A client establishes a brand new TCP connection, does a TLS handshake, encrypts all that jazz, and

now sends a request.

This is a normal HTTP request.

That means it's a request response.

We accept.

We send a request.

We expect back a response.

So it sends it to the nginx decrypts it looks at it, decides what to do with it.

And now we're doing a load balancing.

So it means oh this request let's say round robin.

We're doing round robin.

So this request goes to the we're going to pick the first connection.

Send it to that server.

And the server will respond back.

And then we get it back.

And then we respond back to the client.

The client sends another request.

Oh we remember that the last time we picked this server.

This time we picked this server.

Boom.

Server responds back to us and then we respond back.

Let's take an example of how layer seven load balancing with WebSockets really work.

Once we start nginx.

Nginx is okay with a WebSocket kind of a load balancer.

So let's just wait and see here what's going on.

It doesn't open back in connection.

It might it might not.

It really depends because WebSocket is more expensive than normal HTTP connection.

Because once we open one it's going to become private.

So let's give it this way.

The client makes a TLS handshake.

said, okay, we're gonna.

Hey, I want a connection.

Sure.

And then it follows it up with a TLS handshake on the back end says, okay, let's prepare.

I'm going to open a TLS handshake on the back end.

And then the client sends a request to upgrade the connection.

And then here's what happened immediately.

We will tunnel to the back end because this is a web socket request.

And now anything we get back from the server, back to the client.

The client sends back the connection here.

Send it back.

The server responds back.

Send it back.

The server sends another information.

This is not, by the way, not a request response.

This is just WebSockets.

Yes.

In this animation I happen to do it this way.

But here's another thing.

Server just sends random data.

We just send it back.

It's a tunnel.

This connection now tunneled to this, and you should never use this connection for

anything else except that client.

So if you have n number of clients, you're going to have n number of backend connections reserved.

There is no pooling.

Unfortunately there is no sharing or anything like that.

And then if we ever requested a new connection, then that session becomes a load balancer at that level.

The load balancing is at the connection level.

And if this styles another connection then we're going to be tunneled to this server instead.

We don't send every WebSocket message to a different server.

It's going to fail because okay what is that.

I can't do that.

I don't understand what is that?

Because if you send that WebSocket message and all of a sudden send it to another server,

the server is like, what is that?

You just sent me something that I don't understand.

In the middle of the connection there is an order that needs to be maintained and this order

is a stateful.

So we all bets are off.

We always send it to the same server.

Now you can build a true.

Message by message load balancer at the WebSocket level, but you have to build it from scratch yourself.

If you understand the context that each message really doesn't matter.

I have a back end server that I'm talking to, to another participant.

I have a centralized server message where all the messages pours in.

You can do something like that.

It's more effective if you do this that way.

But again, it depends on the use case.

You can do so many tricks if you understand what's going on first and you really understand what you

want.

