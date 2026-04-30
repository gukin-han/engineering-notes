---
date: 2026-04-30
tags: [backend, networking, proxy]
---

# 51. Proxy vs Reverse Proxy

Guys, as you started watching the course and asking questions, one common question that I started

to notice is people asking what's the difference between a proxy and a reverse proxy?

While this is purely a networking course, I really thought that it would be nice to explain

proxy and a reverse proxy because this is the bridge to the back end, because if you're a backend engineer,

you have to understand the difference between a proxy and a reverse proxy.

This lecture exactly does that.

I'm going to explain what is the meaning of proxy.

And this large that I have here is a little bit of a high level, but I'll talk through the low level

details when it comes to networking.

I'll try to bridge that gap as much as possible.

And then I'm going to explain the usefulness of the proxy.

Why are using why do we use a proxy?

Finally, I'll talk about what it is, a reverse proxy.

And then in that case, what is it used for that we're going to mention API gateways, load

balancers, they all either this or this, sidecar containers, things like Envoy

Linkerd, they all either fit as a proxy or reverse proxy, and that's the state we want to get in.

We want to be able as much as possible to have a few basic fundamentals and every fluff that is being

uttered out there in the software engineering and the network engineering world, we want to be able

to resolve those to these basic fundamentals.

Because any word that you hear means something, and the sad part is marketing

folks unnecessarily blow things.

So you'll hear a lot of things that confusing at times.

And that's what this goal of the course is to not confuse, if possible.

Let's jump into it.

All right.

Let's explain the difference between a proxy versus a reverse proxy.

And I have these slides already laying around, so I just use them and change them to a theme.

If that is a little bit animation that is distracting, I apologize.

This is slides that I have already laying around that I built a few years back.

So what is a proxy?

The definition of a proxy is it's a server that makes requests on your behalf.

By that definition, it means that you, as a client, want to go to a certain destination.

That's your final goal.

You want, for example, to go to Google.com.

But if you use a proxy, the proxy will go there for you.

That's what it means at the high level.

This is a networking course.

So we're going to explain what does that mean?

If I want to go to a google.com, what happens here is I know my final destination, Google dotcom.

But I also know in the client machine that I have a proxy configured and you can look at that if to

check if you have a proxy configure and if you have a proxy configure, what happens here.

From a layer four perspective, your TCP connection is being established not with Google but with a

proxy first.

So you can establish a TCP connection between you and the proxy and the content of Layer seven will

go to Google.com.

After you establish the TCP connection at layer four here, you're going to send the GET

request and they get request will clearly say Google.com, go, go, take me to Google dot com.

And so the proxy would receive that get get slash Google dot com and it will turn around and establish

a brand new TCP connection between itself and Google or com so Google dot com knows the IP address of

the proxy it never sees you as a you as an IP address but the content as you transmitted it is

completely rewritten and written to the new connection here.

So any application seven data is sent as is some proxies ad in the case of HTTP they add their

own headers, things like ex forwarded from and stuff like that.

And if those headers are added, the original client can be known from layer seven.

But from layer four, Google only sees the proxy.

Does that make sense?

You might say, what is the purpose here?

That's the statement here.

I just know that I received a request from my proxy.

That's the only thing I know as Google dot com.

So in a proxy configuration, the client knows the server but the server doesn't know the client.

Yes, there are.

There are exceptions when the proxy adds a header that identifies the client, but that's

cheating.

So that's what a proxy is, you might say.

What's the purpose of this thing?

Why are we doing this?

This is useless.

One use case is anonymity.

I don't want to be identified from an IP address point of view.

I don't want anybody to know my IP address.

So I want someone to write that request in front of him instead of me and send it to this destination.

That's not really secure because of the proxy.

At the moment.

The proxy adds your IP address, you're done.

So you need to trust the proxy in a way.

Caching is another way.

A lot of proxies, especially the old days, I don't think this is happening now, but if you use

it as an organization proxy, that means all HTTP request goes to your organization proxy first.

Like this.

If you want to go to Google, it goes to this thing.

The proxy can choose to cache.

If a sees someone went to this static page, it can choose to cache it.

So if someone else from the same organization using the same proxy hit that server, it will serve

it from the cache.

And that's different if you think about it.

And this is slightly different from we're going to explain the difference because this is

a reverse proxy.

We'll come to that.

But this is another use case logging, all these sidecar containers and service

mesh purely rely on the idea of a proxy where you have an application here.

The site.

The software is installed as a proxy next to the application, as a sidecar, as they call it, and

the application is configured to use this as a proxy.

That means if you want to connect to service A or service B or A service, see, all these requests

goes through the proxy.

This is very valuable.

So caching kicks in.

We don't really care about anonymity here because in service service mesh architecture, we just the

proxies to talk to each other in a sense.

So we can log all these.

We can see how long a request took.

You can measure this, you can monitor, can do all these things because all of this pooled

into this proxy before it's sent to the actual destination.

So the proxy takes care of that logging.

You might argue that, hey, isn't that slow?

Of course, there is a cost for everything, and that's what I want you to do.

Anything I say, hey, I want you to challenge it.

And that's the beauty of engineering here.

There's nothing that you can take for granted.

Anything I say can be wrong.

Because anything my experience is, is my own experiences.

And any one out there can be wrong.

And it's beautiful to think of it this way, because this way you open your mind to critique.

And as a result, if we critique, we get better.

We can build better softwares.

So that's another thing here.

So this idea of service mesh is primarily using log in tracing all that stuff, block sites.

So this is definitely used to block websites.

Back in the day at least.

If you have an organization that uses an HTTP proxy, it looks at all the sites that you're visiting and

it can choose to block.

So say, hey, hey, hey, can you talk me to this side?

Yeah.

No, sorry.

You are not allowed to go to that site.

And just the fact that you're not allowed to go to that site, that means that proxy sees the

site you're looking at.

And this is maybe something I'm going to talk about.

Like the TLS and decryption.

Most HTTPS proxies sometimes decrypt that traffic.

I forgot to add one here, which is a debugging.

If you ever used a fiddler or man in the middle proxy to monitor your requests, you

want an application to see what request is sending.

Fiddler is a proxy.

It is installed in a machine and you configured it to decrypt that traffic.

You configure it so that all the request goes to it.

So you can clearly see all the requests that your application is sending a very, very popular monitoring

and debugging tools.

So that's another thing.

Logging, if you want debugging is another one.

So proxy is very useful microservices, as we mentioned that already because it digs into logging

and caching, which is the idea of service mesh here.

All right, what is this, a reverse proxy?

So we said the proxy in the proxy case, the client.

Let's go back to the proxy in the proxy case.

The client knows the server.

It knows, hey, I want to go to Google or take me there, but the server doesn't know the

client.

That's what a proxy is.

In a reverse proxy, it's exactly the reverse.

The client.

Doesn't know the final true destination server.

It talks to Google.com as its final destination.

But Google dot com could be a reverse proxy.

It turns out it talks to another Google server that you have no idea that you're talking to.

So you as a client, you just know that reverse proxy, that's your final destination.

But Google.com turns around and sends the request to an actual back end server.

So this is called the back server.

This is called the.

Sometimes they call it the front end server, sometimes called the edge servers.

And just like that explosion of beautiful use cases are born.

You might say what kind of things that can be born.

Hey.

Here's one example.

Load balancing.

If I made a request to google.com, I only talk to the same server.

But google dot com could choose to say, okay, let me send this request to this server.

Google can choose to send the request to the first server, the second request to the third, and it

could round-robin through them as effectively load balance through them an even better google.com

based on the path that you're going.

It can take you to different servers.

So let's say you're building a microservice Gateway or API Gateway.

Let's take an API gateway.

So if you're going to slash, for example, post this is the post server, it has a database and everything.

So we take you there if you're going to slash read messages, this is the read server.

This way you can have completely different servers, completely different databases.

This is a read intensive database.

This is the write-intensive database.

For example, this is a row store.

This is a column store for analytics.

And you can do all these sorts of genius stuff just by the beauty of reverse proxy.

So a load balancer is a reverse proxy, but not every reverse proxy is a load balancer.

A reverse proxy is just that is it makes a request to something on the backend that you don't know about.

So that's a reverse proxy.

Very powerful.

So how does it work behind the scenes?

You establish a connection between you and the reverse proxy.

So the TCP connection is here.

So that's the destination IP address.

So the reverse proxy knows you.

You do not know the final true destination.

You never know the actual server that will serve you.

So that's the difference between a reverse proxy and a proxy.

I use this decision for almost six years and I absolutely love it.

Everybody adheres.

This usually clicks with them.

So again, a proxy, the client knows the server final destination server.

But the server doesn't know the client.

In the reverse proxy case, the client doesn't know the true final destination server effectively because

the reverse proxy talks to that in the back end.

And if you flip this, you can see clearly that this is almost like a reverse proxy.

So we're going to establish a TCP connection here and the Google turns around and establish

an actual TCP connection to the actual back end.

So you can have configuration where you are using a proxy and a reverse proxy at the same time.

Like here.

The client knows the final destination.

But even that, if you think about it, that final destination could not be it.

Because Google itself could talk to an act to something else here.

So that could be a reverse proxy.

But the client is using a proxy to talk on its behalf.

But from layer four perspective, this is my final destination.

From layer seven perspective, this is my final destination from layer four perspective, this is my

final destination.

And from Labor seven perspective, this is my final destination.

From both cases, this is my final destination.

I have no clue what's coming after that.

And that's the difference.

Maybe you can look at that this way and it can pan out the real nicely, I think.

What are the use cases?

Caching, same thing, CDN content delivery network.

I call that a content delivery network like Fastly stores the content right here and you talk

to CDN as if it's your final destination.

You have no idea what the CDN is going to talk to, but that could be a server in India and you

could be a client in India, but you're going to connect to that CDN in India, that server in India,

TCP and layer seven as well, application and getting serve content, but that reverse.

You can talk to a server in America.

It completely transparent from you.

So you are served content.

So that is just a glorified reverse proxy load balancing.

I can choose to balance your request from to multiple to multiple servers on the backend.

Ingress like we talked about this API gateway configurations like hey, any API gateway authentication

happens in this application and this reverse proxy can add a deployment where you can do things like

you can test.

I wanted to test this new feature, so maybe you can explain it this way.

So let's say you want to test a new feature.

So you want you will, you will deploy it on one server, but the rest of the server will have the old

version.

And you can add a reverse proxy rule here that says, okay, 10% of the request goes to this new server,

while 90% goes to this.

You can do that.

It's really simple to code.

Any request you can for every turn request take.

One of them told us 10%, one of the requests for everything requests.

All right, let's say hundred for every 100 requests.

Ten of them should go to the server, 90 should go to this server, and ten of them will experience

the new feature.

The 90% will experience the old behavior.

We have to build our application a stateless manner, So that it doesn't break because

that request might go here.

The second request might go here.

So you need to code around.

It's not that simple.

And micro services.

So these are some of the use cases of the reverse proxy.

Very, very powerful concept.

So these are some most of the common questions that I receive all the time about these two things.

Can proxy and reverse proxy used at the same time?

Yeah, but you would never know it.

You as a client will know you have a proxy because you have to configure the proxy in your configuration

by reverse proxy, you don't know.

You where you might hit could be a reverse proxy that talks to something else like

nginx.

And nginx is the reverse proxy. HAProxy.

Is that a proxy?

Can I use a proxy instead of a VPN for anonymity?

It's not a good idea because some proxy is terminating TLS and looks at your content.

VPNs do not VPN operate at IP level, so any IP packet, they encrypt that and they don't know or care

what is in that IP packet.

Proxy operates operator layer four and above.

So they need a protocol.

As a result, the proxy needs to know the protocol that you're using.

So if using it, that's why there is an HTTP proxy, TCP proxy, SOCKS proxy, all

sorts of proxies as well.

Is proxy just for HTTP traffic?

Not really.

There are many types of proxies, but the most popular is the HTTP proxy.

There is.

There is.

If there is a mode, when you use a HTTPS proxy where you can effectively use a tunnel mode where the

client can ask the proxy to open a connection for it.

Maybe you can explain it this way better.

So a tunnel mode in HTTP, tunnel mode, or the CONNECT method, the client can ask the proxy.

Hey, connect me.

It will send you a packet initiative packet, which to be a request that says, Hey, connect to Google.com.

So the proxy will establish a connection and it will that connection will be a dump pipe.

And that just by the fact we're doing that, any segment that is sent is immediately written to that

packet.

So the proxy in that configuration does cannot see the content.

It's a tunnel all the way in to the end.

So in this case, you can do the TLS connection in an end to end fashion.

So the proxy doesn't decrypt.

That's just a bonus content, if you will.

All right, guys, that's it for me today.

Hope you enjoyed this lecture.

And the next lecture, I'm going to talk about the different types of proxies and reverse

proxies like layer seven and layer four, a little bit more in detail.

Looking forward for that.

See you in the next one.

Goodbye.