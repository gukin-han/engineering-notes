---
date: 2026-05-20
tags: [backend, communication, sidecar]
---

# 16. Sidecar Pattern

The sidecar pattern.

So I also hesitated to add this to the design pattern section.

But I think this is not really a special thing, but the architecture of the sidecar pattern really, really talks to me.

I think I liked it and it goes to a problem that happens when you use protocols, right?

When you use the HTTP protocol or HTTP/2 or gRPC or Apache Thrift or SOAP or TCP or plain UDP, you need to speak that protocol's language in order to speak that protocol's language.

The library that you're using needs to understand how to write to a socket in a specific protocol language and needs to understand how to read.

And it gets really complex if the protocol is complex such as gRPC.

Right, or HTTP/2 or HTTP/1 or HTTP/3. The clients are isolated from this.

The library of HTTP doesn't change.

So if you're sending a fetch request or an Axios request, you don't care how the protocol is laid down behind the scenes because they said they're going to make HTTP forward and backward compatible whatever language you are.

If you wrote code that sends a request, a higher-level write request, then that code should work in HTTP/3, right.

At least from a JavaScript perspective, does that make sense?

The library that is being used in the browser understands how to talk HTTP/3 versus HTTP/2 because it's different versus HTTP/1, definitely different under the wire.

And these libraries and these clients become really thick and the backends are even thicker when you have all this complexity.

So the sidecar pattern was born to solve this problem.

So let's talk some examples here.

Every protocol requires a library, it's a must.

This is a must, even if it's HTTP/1.1, right.

If you have a Python app, you need to import an HTTP library to make a call.

You have to write.

Even if it's built into the language, it's still a library.

Right?

Microsoft has a library, you know, C# has a library, C++ has a rich HTTP library.

Python has a lot of libraries.

They should give you one library.

So this library lives usually within the app, right?

And then you make a request and it understands the logic that you write.

You're writing a GET, you're making a GET request, right?

Simple GET request.

This gets translated into completely something different on the wire.

The thing we talked about, a GET slash protocol and then the headers is written this way, and then you measure the size of the body and then you write it this way, and then you end the request in this way and then you do all that stuff, right?

curl is a perfect example.

curl is a client library and it knows how to talk HTTP/1.1, right, in a specific manner.

The server side wants to receive the request.

The library kicks in and parses the request and then moves that request back to the application for processing.

Same thing with HTTP/2.

You need an HTTP/2 library.

Right?

Your library will probably have both HTTP/2 and HTTP/1.1.

So if you are an HTTP/2 client, if you say, "Hey, I want to make an HTTP/2 call," you actually can specify that sometimes. As the application author the client makes a request but the library can make the decision for you to support HTTP/2 and say, "Hey, I'm going to do HTTP/2 by default," and then they negotiate with the server.

And that's another whole thing — how to negotiate which protocol to pick.

That's another art by itself.

And so the client will offer, say, "Hey, I offer HTTP/2 and HTTP/1.1, it's up to your server, you pick," and the server will pick.

This is done usually through a TLS extension called the ALPN, Application Layer Protocol Negotiation.

I know that is a lot of information, you guys, but I try to keep it to a minimum.

But again, go through the course at your own pace.

There is no rush or anything here.

Take your time and ask a lot of questions and the server will respond back saying, "Hey, let's talk HTTP/2," and then they start talking, which is a completely different protocol on the wire than HTTP/1.1, completely different.

The whole thing streams and stream numbers and magic.

And very complex things, right?

So every protocol requires a library, talks to the same thing.

The way we encrypt.

Like you might have a TLS client, right?

Let's say you're going to do an HTTPS request which uses TLS.

You need a TLS library.

So that library, the HTTP library must use another library to do TLS.

Right.

So you're going to have a bunch of libraries.

One popular TLS library is OpenSSL, if you know it, right.

There was a big leak.

It's hard to believe that happened, right.

And that's another problem, right?

If you use something that you don't know, if you use something — chances are if that something got compromised, a lot of users will get affected.

Look at Log4j, what happened.

Right.

Same thing.

Library.

Use a library, you are at the mercy of the library.

Right?

So, yes, you can use a TLS library to encrypt.

Right.

And then OpenSSL to do all the crypto stuff, cryptographic information.

Then you send that and then there must be another TLS library to decrypt this.

It doesn't have to be the same TLS library.

You can use a Rust SSL, I think it's called Rust SSL, and the server can use that to understand because TLS is a protocol, right?

Whoever built that library will adhere to the protocol semantics and they better be, otherwise compatibility issues happen.

And then the same thing.

gRPC, you have to have a gRPC library on both sides.

This is an IDL form.

Usually this is how they are.

The IDL basically built gRPC.

You can build things and then have that as the library for each language.

That's how gRPC works effectively.

We're going to show that as well.

So changing the library becomes hard.

Like what happened with Log4j, right?

If you have a problem with OpenSSL, updating is hard, especially if it's entrenched really hard. Once you use it, the app becomes entrenched, right?

App and libraries should be the same language.

That's another thing.

If you have a language, right, you're writing a Python app you must use — I say must, it's not really the case, but most of the case you should use a library that has a Python package.

You saw me, right?

I wrote a WebSocket application in Node.js.

I used a Node.js WebSocket package.

Right.

It has to be the same language, right?

It doesn't have to be.

You can play tricks with a C API, do some interop shims and stuff like that.

But most of the time the app and the library you import have to be the same programming language.

Java, Java. C#, C#.

All right.

There are tricks to avoid that, but it's out of the scope of this course.

So changing that already requires returns if you change the library to another library.

So there's limitations — it has to be the same language.

And there is also another limitation where it requires retesting.

Retesting is hard, right?

Breaking changes, backward compatibility, all the stuff I have to do, right. Adding features to a library is hard and as a result, microservices suffer.

And that's one way microservices became popular.

That was one of the problems they tried to solve.

Twitter started all of that, I think, back in 2010.

I believe in the World Cup 2010.

We were tweeting.

I was like, "Yeah, go," and Twitter would go down.

It was funny because Twitter was really, really popular back in 2010 ish, right?

And their backend would not handle the load in the World Cup because everybody was tweeting at the same time.

They had VMs.

And again, I took this from a podcast from one of Twitter's employees.

I'm not making this up.

I didn't work on Twitter.

And what happened is the VM would just crash, go down.

So they had to spin up another VM to do the load balancing.

It just takes a long time and that other VM will crash as well.

So they moved from this monolith VM into microservices.

They broke the Tweet API alone, the Read API, the other APIs.

They broke it into many services and then they found that they need to talk to each other.

And so they built their own library called Finagle.

And that library is what they use to basically talk to these microservices.

And this library basically is — everybody must use this library and they are forced to use whatever language there is.

I forgot what languages, but they're fixed to that.

And if you make a system like that, you're stuck with that system and that's fine.

If it works, it works.

And Twitter apparently works, right?

There is a trick you can play here.

What if we delegate the idea of communication to another app?

So what if we let a proxy communicate instead of us?

Right.

So the proxy has the rich library, right?

And the client has a very thin library.

That talks to the proxy.

That never changes much. HTTP/1.1 never changes, which is a beautiful, elegant design, never changes.

So we can talk to the proxy in HTTP/1.1 and the proxy is free to talk to whatever language or whatever protocol it chooses to the backend, to the actual final destination, which is, by the way, another proxy.

Right.

And that's the trick here.

So meet the sidecar pattern.

So in this case, each client must have a sidecar proxy.

Right.

And here's a simple example here, but let's say we have an HTTP/1.1 client here and it needs to talk to an HTTP/1.1 server, right?

So what we do is we're going to configure a proxy in my application such that, "Hey, all HTTP requests, please direct them to this sidecar proxy."

And the reason it's called sidecar is because it literally lives within the same machine.

So this is loopback.

This is also loopback. You can have two applications, almost like another application running in the same server, same machine.

And that's how Fiddler, for example, and Charles and these debugging man-in-the-middle proxies work, right?

You just literally spin up a proxy and then have all the HTTP requests go through the proxy so you can log them effectively, right?

That's how the proxy thing works.

And then this.

So the final request is actually you're going to this guy, right?

This is the final destination.

But because you configured the proxy, all requests by default will go to this guy, all requests will go to this guy.

And so you send the request, the sidecar proxy will receive that request and will know the final destination from the host header.

There is a specific header that has that and then it will do that and says, "Hey, since you are still on HTTP/1.1, you don't really care how I communicate with the actual server-side reverse proxy."

Right?

What you do is, "Hey, I'm going to do HTTP/2. I'm going to use secure connection. I'm going to use the latest TLS library, 1.3, all that jazz."

And I'm organizing the request.

And on the server side, the sidecar proxy receives that and it knows that, "Oh, if someone talks to me, that means this is the actual server that I want to talk to."

And this again lives in the same machine.

Doesn't have to, but most of the time it does.

Like this is where also, if in a container architecture, it's called a sidecar container, which basically shares the loopback with the host with the application.

And that's the trick.

It doesn't have to — you can put it in another container, you can put it in another IP address and configure that.

It's just easier to use loopback because loopback doesn't change, right, versus IP addresses change and there's the threat.

So what you did is like you sent a request but it went through two hops and the communication is completely isolated from you.

The app doesn't change.

You're still making a request in this case to the final destination.

But all of a sudden, because we put a proxy, it works, right?

Because the client-side proxy will capture everything and it will rewrite the request.

And so the client-side proxy actually sees everything and this guy sees everything as well.

So you might want to trust these guys, of course.

So we just got a brand new protocol without us needing to actually upgrade anything.

So whoever owns this will do the upgrading.

This is how usually service mesh — service mesh is based on the sidecar pattern where this is like Envoy or Linkerd, you know, where these proxies talk to each other and they do everything so that if the server actually responds, it responds back to whoever made the call.

And whoever made the call to the server is actually this proxy, right?

So it will just respond back.

Right.

And that's just another client-server request-response system, right?

It will make the response and then take that.

And then because we're responding, the reverse proxy here will just take that and then write back to the same connection that made the request, right.

And then once we get that, the client in this case will just pull back.

So everyone was effectively waiting — this made a request and until we got back the response that the proxy made all this stuff until we got back.

Very elegant.

Absolutely like the era where you shield the responsibility to someone else.

Brilliant.

"Hey, I want to move to this HTTP/3 thing." Easy.

You don't have to change anything.

Your code stays identical.

The reverse proxy here, these sidecars, they have to upgrade and only they upgrade.

And that's how they sold us, right, on microservices and then service mesh.

The service mesh architecture says, "Hey, upgrade to Linkerd 1.3," and all of a sudden you have HTTP/3 secure and you get the QUIC protocol.

How magnificent is this — by just upgrading this, you just remove the connection here and then the same thing here.

So let's talk about sidecar pattern examples, service mesh proxies.

As we talked about, Linkerd or Istio, the Google one. Envoy, the sidecar proxy container.

Yeah, as we talked about, you can spin up the sidecar proxy as a container by itself.

But here's the thing, it has to be Layer 7, right?

And we're going to talk about Layer 7 versus Layer 4.

This is very critical.

And then in the protocol section, we're going to talk about Layer 7 proxy and Layer 7 load balancing.

What is Layer 7?

What is Layer 4?

Because this you got to know, understand these things.

The OSI model is one of the most important things when it comes to backend engineering, in my opinion at least.

Right.

So it has to be Layer 7.

I mean, it is an application thing — that means it decrypts everything, it sees everything and re-encrypts on the back end and it's basically your application.

So what's the pros and cons?

The pros, it's language-agnostic.

It's what they call a polyglot architecture.

Right.

In this case, if you have two services that you force them to use a library, then everyone should use the same language because whatever that library is must be the same language, right?

Twitter with Finagle, whatever that language, I think.

What is it, Java or Scala? Scala, I think it might have been Scala.

Right.

So yeah, everyone is forced to write in Scala.

That's it.

You cannot write in another language because, hey, guess what?

To make connections, to make networking requests, to do all these retry logic, all this beautiful circuit breaking, all this special logic, because it's not just making a protocol, right?

You can do all special networking logic in this sidecar proxy, anything networking related, anything connection related, anything request retries — can do it there.

But if you're stuck with a library, then you're stuck with the language, right?

But in this case, your language — you can build a Python microservice that talks to a JavaScript microservice. It doesn't matter why?

Because the sidecar proxy is built in, let's say, Rust, write Linkerd uses Rust, and these guys talk to each other.

I don't care.

We're going to use an HTTP/2 connection anyway between the proxies, right?

So it's language-agnostic.

You get protocol upgrade any time. You get security, even if you're insecure, you can get TLS executed.

And in case one TLS library is found to have weak ciphers, you configure the proxy, "Hey, don't use that cipher anymore," you're done.

And every single application gets that latest change.

Pretty cool. Tracing and monitoring — you can trace requests because now everything goes into this same funnel, right?

And these sidecar proxies talk to each other and they have something called the control plane, another centralized thing that controls pretty much everything where you can have tracing, you can have monitoring, you can control, "Oh, this service actually cannot talk to this service. This service can talk to this service."

Right.

And you can monitor, you can trace requests by tagging requests with a special tracing ID so that you can follow it across the entire architecture of microservices.

So if service A talks to service B and B talks to C and C talks to D and D talks to E and E talks to F, you send one request and you want to know how long it took — tracing.

If you're using service mesh, you're good. If you're not using service mesh, forget about it.

Tracing — this right.

You have to teach all the services to understand what this tracing ID is.

But if you're not — if you don't care, then using the sidecar pattern, the sidecar proxy, will add the trace ID for you and it will just follow the request all along and it will make sure to repeat that trace ID.

Service discovery.

Right.

Another thing where you can discover the services, right.

Using a centralized DNS system and all of these.

Proxies will talk to the service discovery.

You don't even need to think about it anymore.

Caching — the sidecar pattern can do caching again, right?

Which is very interesting.

Right.

Although these are all things that have to be done.

But of course, that's complexity.

Nothing is free, right?

I know I'm out of — I think I'm out of pros.

All right.

Now cons.

What's bad about it?

Complexity.

We just talked about it.

It's a complex system and complexity means that it effectively struggles to understand all of this, struggles to know if things are broken.

Right.

How do you debug this thing?

Good luck debugging a microservice.

Latency.

You just added two hops that didn't exist, right?

With each call, you added one hop to the proxy and one hop to the reverse proxy, which goes back to the server.

Although these hops are local, right, they're still there. There is the cost of rewriting the request, understanding the request and doing the protocol upgrade.

Doing all that stuff.

Doing the trace, the service discovery, doing the caching, doing the tracing, all this — there is cost for everything. And then this ends our sidecar proxy lecture.

And I think this is the final lecture of this section.

Of course, as always, I might add more lectures in the future, but this, as of recording of this course, this is the final lecture of this section.

I'm going to see you next time in the protocol section.

Meanwhile, enjoy the course.
