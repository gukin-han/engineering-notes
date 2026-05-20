---
date: 2026-05-20
tags: [backend, communication, request-response, design-pattern]
---

# 07. Request Response

Let's start with the first and the most famous communication design pattern for the backend: request response.

Very elegant, very classic.

And it's literally everywhere.

If you think about it, you make a request, the backend processes the request, and it responds back to you.

I can literally make an entire course just talking about request and response because there is so much going on in this request response.

You know, you need to kind of remove the layers and discuss more, but maybe not a lot of time.

Let's talk about request response.

What is this really?

So in a request response model, the client sends what we call a request.

Even the concept of a request, we really need to define what that means.

What does that mean?

Right?

If I am in a network environment, if I'm sending a request, the client really needs to define what a request is.

And guess what?

The backend really needs to understand what a request is, because you see, the data that you send is not just one thing.

It's not like mail where you send one thing.

It's a continuous, usually in TCP, it's a continuous stream of data, right?

And the server needs to parse the data to look for the start of the request and the end of the request.

We're going to discuss all of that.

But the concept of a request is very critical here.

And then the server parses the request, as we talked about, because the server really needs to understand the concept of a request, because the client might have sent three requests.

So how does it know that this is actually three requests versus one large request?

Right.

Where does the request begin and where does it end?

This is very critical.

And this is basically where us as backend engineers really need to understand this thing, because believe it or not, the cost of parsing a request is not cheap.

We'll find out soon. There's so much fun here.

We're going to have fun in this course.

Once we parse the request, then we have to process the request on the server side.

Now this is different.

Parsing the request, understanding the request, right.

And then actually executing the request is different.

If you receive a GET request, knowing that this is a GET request is one thing.

Executing the request to fetch an API or query the database is another thing.

So these are the processing aspects of requests on the server.

Of course, once the process is finished, it sends the response back to the client.

Even the concept of response is very similar to our request.

How does the client know that this is where the response starts and this is where the response ends?

The boundary of a response is also important.

Yeah.

And then the client parses the response and consumes it.

Then the client parses the response and then consumes it and does whatever it does.

Displays it on a page, makes another request as a result.

Simple stuff, right?

So the client sends the request and the server responds back with the response.

So these two arrows is exactly what we explain here.

Are there more stuff that I didn't explain?

Of course there are.

For example, some of you might already think about, where does the serialization come into place here?

For example, if I have JSON as a payload or I have XML as a payload or I have a protocol buffer as a payload, I would classify serialization and deserialization as actually processing the request, believe it or not, because parsing a request is just to understand, hey, this is where it starts, this is where it ends.

But then you have to do another step to deserialize whatever this content is to something I can understand in my programming language on the server side.

But there is a cost to serialization.

Like, why do people move from SOAP XML to JSON REST?

Right?

One of the reasons is the expense of parsing XML is way higher than parsing JSON.

Actually parsing JSON is also slow.

That's why people move to protocol buffers as a quick parser.

Yeah.

And this is where plaintext versus binary, human readable thing comes into the picture.

JSON is nice.

I can look at it and understand it.

It smells nice.

I can look at it and understand it.

But the cost is the size.

The cost is the parsing.

We're still in slide one.

Let's move on.

So where is it used?

Where is the request response being used?

Well, it's everywhere.

Technically, the web.

You know, when I say web it's basically HTTP, HTTP is a request response protocol. DNS, it's a request response protocol.

You send a DNS resolution request.

Hey, what's the IP address of Google.com?

That's a request.

It uses UDP.

It puts it in a message, a datagram, it has a query ID, right.

And then when the resolver or the recursor actually finds the answer eventually after going through all the DNS, I talked about this in my networking course in detail.

Once it gets a response, it writes it back to the client with the query ID so that we know that, oh, this thing we just sent, that's the answer for this, because the client can send 100 DNS requests right at the same time.

So how do you know? You don't rely on order ever when it comes to backend engineering at all.

Don't trust order.

Right.

That's why pipelining has been discontinued when it comes to backend executions.

Just can't trust it, man.

Plus, there is head of line blocking, so it's essentially the same thing.

SSH, you send a request to the SSH server and then it responds back with the list of directories.

Request response. Remote Procedure Call, RPC, is a very classic kind of style of programming and it is technically a request response.

You send a request.

And that request is basically a request to execute a method.

But this method happens to be in a remote server instead of the local machine.

So you cross the network to execute this method and then it gives you back the result.

And the reason why RPC was popular is because as a client writing code, as a programmer, I don't know the difference between, oh, this is a local method versus a remote method, I don't care.

And they want this abstraction.

Granted that the moment you introduce abstraction, you introduce uncertainties, right?

Because like, why is this method fast?

Why is this method slow, right?

Because now you have to understand, oh, this method is slow because we send it through the network and that defeats the purpose of me not needing to know that this is a remote procedure call. Leaky abstractions is the worst.

And that's one of the things that you have to be aware of.

SQL. The SQL language is a request response protocol.

You send a query to the database and the database processes.

Your SQL prepares a plan, you know, parses this SQL, prepares a plan for execution, says, oh, an index is good for this.

A bitmap index is very good and index only scan is good and all.

You don't want a full table scan.

You're asking for a lot of data, full table scan, and then it actually does the execution, queries all the data from the tables and then builds the response and then responds back to you.

Request response.

APIs, all sorts of APIs, REST representational state transfer, SOAP simple object access protocol.

Rarely used, but still used in enterprise systems.

I've seen an enterprise system or two that uses SOAP. If it works, it works.

I always say that unless you are running into deficiencies, then you try to change.

Don't try to prematurely optimize things that don't need to be optimized. That's always my rule of thumb.

GraphQL very, very popular and recently developed actually by Facebook.

And the idea here is just packaging different requests into one so that you don't be chatty, because that's another kind of limitation when it comes to request response.

If you're needing to make a lot of requests, then you are taking the hit for each request and response, right?

GraphQL tries to solve this problem, which actually exists in REST, by saying, hey, give me this user and give me its comments and give me its full name, and also give me everything that they said in between these dates. And in REST, the architecture is, you have to make multiple requests.

Because everything we describe is a resource.

And GraphQL says, hey, send me a language specific syntax and we'll take care of it in the backend.

So GraphQL technically still makes multiple requests, queries onto the database.

It depends if you created a view or not.

But what GraphQL does is actually moves the multiple requests from the client to the backend, to the database.

Technically, right. But also because it knows that context, it can eliminate some of the SQL queries on the backend if it knows, right, and if configured correctly. And it is implemented in a lot of variations.

So let's take a look at the anatomy of a request response.

How does it look like?

A request structure is defined by both the client and server and they have to agree on it based on the protocol being used.

Right?

So if we take, for example, the GET request, GET slash the path and then the version of the protocol followed by the header by the CRLF carriage return and then the actual body. Well, GET doesn't have a body, but you get my point. In general, this is how it looks like. The server actually understands this, parses it and gives you back a response.

Right.

So there is a structure that has to be agreed upon.

When you do a request response, both the client and server have to agree and you have to write code to understand this thing.

This work is being done for us.

We technically don't sit there in the backend and parse segments to understand, oh, this is the first course, this is the single course.

It's all done for us as backend engineers by libraries such as the HTTP library.

So whatever library you use, if you're listening on an HTTP server, Express uses an HTTP server for Node.js, for example.

And it does that work, it actually sits there and parses.

So all this work is being done in your app, but it's just another part of your app is doing it.

And guys, this is the goal of this course really. It's just to have a clear understanding of what exactly happens on the backend from A to Z.

From the moment I get the first byte of a request, what exactly happens?

Because you see, not only the code you write is what the backend is doing, your application is doing way more.

All the libraries that you reference is doing work.

And the key of understanding what these libraries do is the key to becoming a better software engineer in general.

So nobody can actually trick you, right, because you actually know what's happening and you can take conversations to any level of this stack.

I know I'm going all over the place, but this is related. A request has a boundary.

We talked about it and it's defined by the protocol and the message format.

So the protocol is where the software ends and the message format itself is also another piece, right.

Which is the serialization and deserialization we talked about.

If it's just text, like HTTP is text, right?

Hypertext Transfer Protocol, right.

So that's easy.

There is no parsing to be done, right.

But it's like if it's JSON, if it's XML, if it's protocol buffer or if it's your custom message format like you built your own binary format.

You can do that.

Then this format has to be understood in both the server and the client.

You need to know how to parse it, and not only parsing it, actually making it back into an object that can be used in your app. You know, in JavaScript, we normally don't notice that, right?

But in C++, for example, if you use JSON as a transfer, there is a C++ JSON parser that takes a bunch of strings of bytes that looks like JSON and then actually starts parsing this JSON and then building it back to an actual C++ structure that actually looks consumable to a programmer.

That bridge.

Right.

And I've seen in applications, dude, some JSON parsers, if the JSON is large, it takes close to 2 seconds to parse.

Right.

That's where you have to really get into like, what is a good parser? What is not?

JavaScript is the best because the parsing of JavaScript is very native to JSON.

There is a cost, but it's fast.

How fast can it be? Faster? That's a different story.

The same thing for the response.

Of course, the response will have a message format.

It will have a protocol.

We have to abide by the protocol and we talked about the HTTP request.

That's how it looks like.

Yeah.

So let's say I'm going to build a backend application that uploads an image.

So a backend image service, I want to upload an image using the request response pattern.

Well, one way to do it is simple.

Take the whole image from the client and literally just send that entire image on the wire to the server.

That's the simplest part.

The problem is there are limits here.

Can you send like a seven gigabyte image?

What kind of image is that?

But you get my point, right?

It's a large file.

There's a limitation to that.

But you can do it.

That's one way to do it.

Right.

The other option is to chunk the image into small portions and send each request with its chunk.

The beauty of this is the old method, the original simple method, is not resumable. So if it fails, guess what?

The backend will get like three quarters of an image and it will just quit.

Right?

Because it's like, oh, it looks like the client didn't send me anything and the connection got disconnected.

So I'm going to delete all this stuff, right?

It's not going to do anything.

It depends what you did in your backend application, but if you did it this way, the other method where you chunk the image and you send it by each request and you mark the chunk with a unique identifier, then the server will say, hey, by the way, in case if you crashed, I got chunks one, two, three, but I didn't get anything after that.

Is that done, right?

You would say, for example, hey, it's actually seven chunks total, and we sent so far three, so you can resume effectively the execution.

This is the trick you can play, but technically it still is request response, but the style of execution changes.

So I can send the whole image, right.

That's one way.

Or I can send portion one, get a response, "I've written this portion." Then I'm going to send another portion, I get this bit, and the third chunk.

And then finally the server in this case will build up all of these chunks in its vicinity.

Right.

And then assemble them back.

This is a very nice approach.

If a client disconnects, a client can come back and say, hey, by the way, I can write some local information that says, hey, I uploaded this and this, hey, you're missing this, and this can ask the server and they can synchronize their state here.

There is no way to do that otherwise, but this is simpler, of course, right?

There is always a cost to everything. Problem is, it doesn't work everywhere.

It's like, let's take a notification service, for example.

You want to build a notification service?

A notification service that says, hey, when someone logs in, I want you to basically tell me that someone just logged in or someone just uploaded a video, right?

Or someone just commented on my story.

Right.

The notification service with request response doesn't really jive, right?

The problem is because the client has to make a request, but in this case, technically the backend has the knowledge.

Right.

And the client doesn't.

So one way to solve it is to say, hey, do I have ID? Do I have a notification? Do I have an interaction?

It works.

That's a request response.

Do I have a notification?

No.

Do I have an ID?

No.

Do I have a notification?

No.

That's called polling, by the way.

We're going to have another lecture talking about it, but it doesn't really scale well. Chatting application do have, did someone just chat? Did someone just chat? Did someone just chat?

You can't do that without request response.

You can try, but there will be a huge latency and you're going to, you know, just spam the network with empty requests.

And these empty requests can congest the network and you have other problems on your hand. In this case, very long requests, let's say very long running requests to be specific.

Right.

So if I send a request and that request just takes a long time to process, I'm sitting here just waiting.

Right.

You can do it, of course, but it's better to use another style of execution than just a request response.

You can make it an asynchronous processing here.

We're going to talk about all of that stuff here.

Like an example is, what if the client disconnects and we have a very long execution request?

The client comes back, it doesn't know.

Hey, is it done? Is it not done, right?

What am I going to do?

What am I doing here?

So there's tricks we can play here.

I'm going to go through all of this stuff and I'm going to solve all these problems with different kinds of design patterns.

That's the purpose of the section.

Yes, we're going to talk about alternatives.

So let's take a look at a visual right here.

You know, where I'm sending a request and I'm processing the request and I'm responding as a backend.

Right?

So this is the server, this is the timeline, and there's the request.

So if I actually mark this with certain times, I started at t zero, and then t two, and then a bunch of time goes by, the time continues to t 30 and then t 32.

The reason I did this in particular because I wanted to show you all the time being spent, right.

So for instance, the client finished writing the request at t zero.

There is something here that as I spoke these words, that is something I missed to talk about.

Writing the request also takes time, right?

So let's say we start at t minus two.

The time it takes to write the request also has a cost to it.

Right?

And once you actually write all the request and prepare it and then flush the changes to the network, that's when we actually say, hey, I'm done, I just sent the request.

That's what it means. I just sent the request.

This area means serialize the JSON to a binary, serialize the protocol buffers from my object to a protocol buffer, all this stuff here, and now we send it.

Look at that.

It's at an angle.

Right?

Because why?

Because the request takes time to arrive.

Right?

There is the cost of the network here.

The network takes time to transfer my request.

And my request doesn't look like an arrow.

Of course, on the network, it looks like whatever protocol I used right in the underneath. Like if I use TCP, the request will be broken into small, what we call segments, and these segments will fit into what we call IP packets and these will be routed in the Internet.

So all of this routing, and some IP packets will arrive out of order and they have to be reordered in the server.

All of this cost is accounted for from t zero to t two.

So the server eventually receives the request and understands it in this time basically. So that's the time it takes.

But then we have the cost of actually processing the request.

So here the client is still waiting. The moment we got the request and actually addressed the request, we went and started to execute.

Up until right here.

Right.

Right here.

t 30.

The server says, oh, I'm writing the response. I'm finished. Let me write the response.

It started writing the response and finished writing the response.

It goes and the execution goes and the transfer goes and the client receives it at t 32.

And these are not seconds or milliseconds, just a unit of time.

And just for simplicity, it could be milliseconds, could be anything really.

But I just want to show you that the time it takes here.

So.

Although we finished execution, the response here, we got it right here.

And this also includes the reordering of the response.

All the packets arrive in order, and you can add extra time here to say, hey, this is the cost of parsing the response and this deserializing back to objects.

Right.

So that's basically the request response.

All right.

How about we do an actual quick demo to show our request response?

What's easier than using curl, which is developed by Daniel Stenberg?

It's a very popular client library.

And we're going to just query a page and we're going to use the dash dash trace to give us as much information as possible.

Let's do that.

All right.

So I'm going to do dash v and dash dash trace.

I'm going to do the output and out the text and then I'm going to do HTTP.

I'm going to specifically use HTTP.

I'm not going to use HTTPS because that's a different lecture.

I'm going to talk about TLS and all that stuff. And let's say Google dot com. I'm going to actually do that.

All right, so let's check out our out dot text file here.

I did cat to show the results, but look at that.

So what we did here is the following.

Let's disappear here.

Just remove myself so we can see more.

And we can look at this.

So what we did first is we established the TCP connection.

We did a DNS to get the IP.

We connected to Google on port 80, and then we started sending the headers and this is how it looks like.

So that's the first thing.

See, the arrows to the right means it's a request, right?

And this request looks like this.

The GET request, the method, the path we visited slash. If I did a slash about it's going to say about here. And then HTTP one one, the protocol, followed by a new line, headers.

Right.

Host google.com, user agent curl, and accepting everything.

And then once we're done with the headers, that's it.

There is no body in the GET request, right?

You guys know this.

And if it's a POST there will be another section to actually send the headers and the body as well.

And then we can see that we got the response.

The first thing we got from the response, and that's how HTTP works, is we get always the headers first, right?

So we got back the headers and immediately the server, the first thing responds back with the headers.

So we got HTTP 1.1 301 moved permanently, says hey, google.com, it doesn't exist, go to this page instead, go to www dot google.com.

So this location will cause the redirect, but we don't redirect by default in curl so there is no redirection.

So that's the final result.

We continue receiving headers.

See, this is the beauty thing here.

It's not like one thing, you get a bunch of bytes and these bytes immediately get processed by the client and says, hey, all this is a header.

Look at that.

And then we just say, oh, another header, and then whoa, another header, and then another header.

We see another header.

So these, all the headers are received, looks like one header at a time, but maybe this is how curl displayed it for us.

Maybe it got it all and then it just shows this to us in this format just so that we can understand it.

But this is like the content length and all the headers are received and you can see the arrows this way.

And then now we actually receive the data.

That's the body, the response.

It's an HTML page of all the contents.

Hey, it's moved permanently. Just go somewhere else.

Right?

And that's it.

We're done.

So it's a request response.

Very simple, very elegant.

Right.

How about we move to the next structure?

I hope you enjoyed this one.
