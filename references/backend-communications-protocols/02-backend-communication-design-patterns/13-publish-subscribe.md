---
date: 2026-05-20
tags: [backend, communication, pubsub]
---

# 13. Publish Subscribe (Pub/Sub)

This is another one of my favorite

patterns, design patterns for the back end communication.

It's called Pub/Sub.

Publish, subscribe and publish subscribers where you have a client that can publish, write, or

a publisher.

They call it publisher.

You can publish something to the server and then move on, and then the client can consume from the server.

The piece that was just published. This is instead of, it was designed to solve a problem where you

know a lot of

services need to talk to each other.

He creating this mesh architecture where all client one, client two, client three has some data it needs

to send to client three, four, five, six, seven, eight.

Right.

So you will have to establish connections with all these clients, and this guy has to work with all these

clients.

But instead, what did you do?

What do you do?

Is all of them just publish all the content to the server and move on, and then let whoever wants to consume

it consume it.

That's the trick here, right?

So one publisher, many readers. You can of course have many publishers as well.

So request response.

Let's go back to the basics.

You make a request, you wait and then you get back content, right?

But does that scale when you have many, many, many servers?

Here's where it breaks.

Let's say this is YouTube, right?

And I want to upload a YouTube video, but technically to upload a video, the video has to

be uploaded. Once this is uploaded it has to be compressed.

Right?

That content has to be compressed.

And once it's compressed, it needs to go to a format service to produce like 480p and then 1080p,

720p, 4K, right.

Encode all these formats.

Right.

And some of these formats will be consumed by other services, right?

So it's almost like a microservices architecture.

It's like, oh, once the 4K is uploaded, go and notify, call the notification service to actually

notify users that an uploaded video was uploaded.

Meanwhile, we also go and call the copyright service to check for copyright infringement for this video.

Right.

All this thing is happening, right?

If you do it as a request response, let's follow as a simple request response where it actually

breaks.

I uploaded a video.

What happens in the back end.

The upload service in this case is just loading.

Right.

And the client is waiting for the upload to finish.

Well, it's not really waiting for the upload to finish, waiting for everything.

So the upload here, once the upload completes it takes and then sends that same file to the compressed

servers.

Meanwhile, the client is waiting.

The client, the compressed server right, will now process this and once it's done, it sends the request

to the format service.

But it's like, okay, the format service to actually format the video.

And then this guy just keeps waiting and doing all this work.

And once it's done, it goes to the notification service.

And once the notification service actually succeeds, it responds back with this, Hey, I'm done.

And then, hey, I'm done.

Unblock, hey, I'm done, unblock, hey, I'm done.

I'm blocked.

If you do it this way, any break in this will break the entire workflow.

And if you do it as naive as this, well, right.

Because anything that breaks can go wrong.

But what if you, like, want to include the copyright service?

How do you do it?

So you have two dependencies here.

So it becomes very, very tricky to do it in a simple request response that these things talk to each

other.

So the request response pros and cons here in this case. Yeah, it is elegant and simple.

It is scalable, it's very nice.

But the problem is that it's really bad for multiple receivers.

If you have a lot of receivers, you cannot really control that in a very elegant manner.

It has high coupling.

These, you know, these services are really highly coupled, and the client have to be, of course,

always running. The client and the server, both of them have to be running all the time, of course.

Right.

And there is, of course, you have to do chaining and circuit breaking and all this complex logic.

That's why we have service mesh and, you know, sidecar proxies and stuff like that.

Right.

So me, what we're going to do is Pub/Sub, the Pub/Sub model.

What we do is let the client upload, and once the upload actually finished, the client's job is

done.

That's it.

You'll get an ID, right?

And that's it.

We upload it.

That's what YouTube exactly does today.

Once you upload it, you're done.

You don't have to wait for any of this stuff.

It can happen in the background.

So what happens in the back end?

The upload server will turn around, and now it will upload the raw MP4 video into some sort of

a topic, some sort of a queue that says, Hey, here is a list of all the raw MP4 videos that

we just uploaded.

Right?

And this thing is what we call the broker or something called the queue, and all the topics live here,

and topic is just think of it like a group of things that customers can subscribe and consumers

can subscribe to.

So now we upload it to a topic here called the Raw MP4 videos, and then once the upload is successful,

we're done.

So what happens here is the compressed service can just consume these videos right from

the topic.

Right?

Without this having to be coupled with this, the only factor is having the broker, which is this guy

in the middle, the server, the big server.

They have to be of course online all the time.

So now the compressor will take that, will do the processing and then guess what?

It will write back to another topic.

Say, Hey, here's a compressed video.

For anyone interested in compressed video, you can consume this topic right here and now.

Whether how this is delivered is up to the implementation here.

This could be a push or it could be long polling.

That's what we talked about, right?

Kafka versus RabbitMQ.

RabbitMQ pushes. Kafka instead allows the client, which is the format service in this case, to actually

pull or long poll whatever is ready.

Right?

And this is where the art of back end engineering comes into the picture.

There's nothing no right or wrong.

It really depends on what you're trying to do.

So the format service takes that, tries to process and guess what?

It writes three topics, right to this, this and this. Produces the 480p version, the 1080p and

then the 4K, of course, the 720.

But I must add to that.

And then once, let's say the 4K, that you have a notification that if the 4K version is ready,

send a notification.

Of course, that might be not a good idea, right?

Because you might want people to watch as fast as possible, maybe the 480, but maybe they know they

won't be happy with the quality.

Right?

So this is where you control your user experience here that, hey, the notification service, the 4K

video is ready, go and notify it, notify your users.

So we have multiple consumers.

One publisher in this particular case. Actually all this, this guy is a consumer and a publisher.

This guy is only a publisher so far.

This guy is a consumer and publisher.

This guy is only a consumer, Right.

So this is how the difference between a consumer and publisher.

I know we started talking about Kafka terminology here, but it kind of fits here.

That's the publisher subscribe model.

These are subscribers.

This guy is publisher.

What's the pros and cons?

Well, the beauty is you can scale with multiple receivers for the Pub/Sub model.

It's great for microservices, right?

It has loose coupling.

So these clients are not really directly tightly connected to each other.

So if you're worried about breaking changes or not, you might not get into this situation.

So that's good.

It works even if the client is not running technically right, because hey, once you upload, once

you publish is done, you move on. Whatever, you already, you can consume at your pace whenever you're ready.

How is the cons?

There is a message delivery issues here.

The classic two general problem.

Where?

How do I know that the consumer or the subscriber actually got the message?

That's a very, very, very difficult problem to solve.

Right.

What if the message got twice consumed?

Twice.

That's what Kafka and RabbitMQ tries to have.

This only one guarantee.

At least one guarantee.

Right?

All this thing is, this is to solving this problem is really difficult, you know, complex.

Of course, adding another broker, adding, making the partitions, of course to scale better.

But yeah, add complexity of also network saturation, at least in the case of polling.

Right.

If a lot of clients are trying to poll, then you're going to have network congestion between clients

and the broker.

Let's do an example with RabbitMQ. I think it's a good example.

I built a little bit of a queue very similar to the thing we built with poly, but instead we'll just RabbitMQ.

I show that example in today's video.

I'm going to spin up a cloud version of RabbitMQ using the Cloud Advanced Message Queue Protocol.

So the Advanced Message Queue Protocol applies for many messaging queues, right?

And one software that uses this protocol, this AMQP protocol, is RabbitMQ.

So in this video I'm going to spin up one RabbitMQ instance on this service, and I'm going to create

a publisher and create a new queue on this instance and then start consuming the same instance from

a consumer that I'm going to build.

I'm going to use Node.js to build these applications.

And this is just to tell you that you don't have to basically spin up these RabbitMQ instances locally

on your machine.

So you can just use one of these services and it's really nice and easy.

And how about we jump into it, guys?

All right, so I'm going to go ahead and log in to cloudamqp.com and we go ahead and create

a new instance, and what we're going to do is going to call it, I don't know, test.

And you can choose any plan you want.

Free is more than enough, right?

I'm just going to go ahead and select Region East, really wherever you want this instance to

be. The closest thing is a west.

I'm going to pick it like, I don't know, North Korea, California.

Sure.

This is the closest to me because I'm California.

Go ahead and review.

So go ahead and create an instance.

And just like that, we have a new server RabbitMQ manager.

You can go to the RabbitMQ manager, take a look that obviously I don't have any 'cause I don't have any

exchanges, I don't have any channels, nothing.

Right?

So all I'm going to do is go to edit and let's take a look at the services.

That's the host and this is the AMQP URL, so I'm going to go ahead and copy this puppy and let's

go to the code guys.

How about that?

Go to the code.

This is the code guys, I'm going to share with you.

All right.

So a publisher, the publisher will publish.

We'll connect to the RabbitMQ server, which currently is set to my local host.

I'm going to change that and I'm literally going to put this huge URL that I got from the server.

Right?

And I can connect to the server, get to create a channel because this is like the stream level, just

like HTTP/2, create a channel, and then we can do stuff in this channel.

We can have many, many channels by default.

I think the servers give you around 200 channels by default.

That's pretty much for a free version.

This is enough, right?

We're going to create a new queue called Jobs, and we're going to send content to the queue based on

whatever the parameter that we send to this application.

Right.

And this can, I'm going to send a number.

And then once this number, we're going to make it into a JSON and then send it back to the server.

And then the moment we send it, we close the connection, we close the channel and close that connection.

At the other end, I'm going to consume that new content right on the consumer side, right?

How about we connect and test this thing?

Node publisher.js and I'm going to send the number 107. Boom.

Just like that.

We have connected.

We have sent the job.

We have created a job queue because it didn't exist, right?

This is what assert does.

And then we sent the queue, and then we closed.

We sent that job, an entry to the queue, and then we close the channel, we close the connection.

Let's look at the dashboard.

How do I go to the dashboard?

Still figuring this thing out.

RabbitMQ manager.

All right.

Look at that.

We got a brand new queue called Jobs.

How about channels?

There's no channels.

Weird, because.

Yeah, because we just closed the channel.

Right?

And yeah, that's the jobs.

There's some content in it.

Probably this is the queue message.

There's one ready.

Hasn't been.

There's no connections.

And yeah, there's all that stuff.

There's one queue, seven exchanges.

I didn't create any exchanges.

These are the default exchanges, I guess, right?

I never used these exchanges in here, so I'm not really familiar with it.

But yeah, let's go ahead and play with the consumer side of this.

It's not consuming this.

We'll take this thing consumer, and yeah, we can just do the same thing here at home.

This connect.

That's.

And what do we do?

We're going to consume same exact thing, right?

I'm going to connect, create a channel.

Assert that the queue exists.

Right?

We don't really need the results.

We're not going to do anything with the results.

And then we start consuming.

And the moment we consume anything, we receive the job with the number, and we print that number.

And that's just, this was some example with the ack and stuff.

I want to acknowledge that stuff, but I'm not going to acknowledge anything.

I'm just going to consume and print.

Let's go ahead and do that.

Node consumer.js.

I'm not going to pass anything else when I do that.

So let's look at that.

Received job with input 107 and the connection is still there.

I did not close the consumer for some reason, Right.

I want to show you that if I go back to the dashboard, that's the queue.

Queue is the high availability.

Queue and all that stuff.

So yeah, and if I kill my queue, my consumer, and I did this, I'm going to see the same thing.

You know why, guys?

Right?

Because we have not told RabbitMQ to actually dequeue that job.

However, while I am working on 107, that job 107, if someone else connected to the queue the same time,

what they will do is let's do it.

How about that?

Actually just node consumer.js, create two connections.

Look at that, this puppy did not get the queue, did not get one also because of RabbitMQ.

Right.

The other puppy is using the 107. Right.

But the consumer didn't acknowledge it.

Right.

So for all we know, if I kill this connection, I go back, let's see what will happen.

Right?

Look at that.

Because the first consumer died.

I go to the second consumer, immediately got the job because they said, hey, looks like this consumer

didn't acknowledge me.

Right.

That's why I acknowledge me.

They are very, very tricky to get.

Right.

All right, guys.

So a very short video to talk about RabbitMQ and how to spin out RabbitMQ in a Cloud.
