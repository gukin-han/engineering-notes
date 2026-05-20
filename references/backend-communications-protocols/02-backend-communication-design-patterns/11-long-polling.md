---
date: 2026-05-20
tags: [backend, communication, long-polling]
---

# 11. Long Polling

All right.

Long polling.

When I first heard about the long polling pattern, it was maybe 10 years ago when I first got interested in Kafka and I was reading about Kafka and heard like, hey, we didn't use the push model and we moved to a long polling one.

I was like, what is long polling?

Then I found out it's a very neat trick that you can do in the back end to avoid the chattiness of short polling, or just polling, right?

So the request takes a long time.

I'll check with you later, but — talk to me only when it's ready.

That is the trick.

So you will make the trick — you will make a poll request.

Same thing.

A request to poll.

But the server just doesn't respond.

Doesn't write anything to the socket.

Right.

Instead of saying immediately, hey, it's not ready, false, it just waits there.

So the client is asynchronously doing its own thing because it sends the request asynchronously.

All clients do that these days, right?

But the server also, since it knows that the job is not ready, the response is not ready, it will just say, okay, I'll just do my own thing and I'll come back later.

Right.

So it got the first request, got the job ID, and then the next request to do long polling — it will block effectively, if you will, if you can use that word — the server will just not respond.

Let's take an example here.

So one request, one response.

Our ideal request takes a long time, or — same thing, right?

Same thing.

With that polling.

You are uploading a video, you're uploading a long video and you want to check for notification.

You want to check if the user is alive or just log — sure, polling is good, but it's chatty.

Meet long polling, and it is being used for Kafka effectively for clients to consume a topic in Kafka.

They do a short polling — the consumer, they call it the consumer — the consumer will do a short polling.

Sorry, long polling.

The consumer will do a long polling request and Kafka will not respond immediately until there is an entry in the topic.

Right.

It will then respond back.

And that's the beauty here.

So we're going to simulate this with an example in a minute.

So what is long polling?

The client sends a request, the server responds immediately with the handle, just like with the same thing.

Right.

And the server continues to process the request and the client uses that handle to check for status.

It doesn't have to be like — I'm giving you an example here where there is a request and you submitted a request and you're processing the request, but it doesn't have to be this way.

Like for Kafka, what they do is, you know the topic, right?

The topic is already there and you want to subscribe.

The consumer subscribes to a topic and the subscription works by long polling.

That means, hey, I want to connect to Kafka.

And the consumer sends a request.

Saying, is there any topics in this topic?

Is there any entries in this topic?

They call it messages, I believe they call it.

Yeah.

Are there any messages in this topic?

Right.

And if there aren't any messages for that partition in that topic, Kafka blocks.

It will never reply.

So technically the client just keeps waiting.

So you did not really waste bandwidth by sending multiple poll requests.

The moment Kafka gets a message, it writes back to the client's response.

And that's how we call it long polling.

You're polling and it takes a long time to poll, instead of short polling, where the server, the back end responds immediately.

The server does not reply until it has the response.

And that's the trick here that I'm covering.

But it says we can disconnect and we are less chatty.

That's basically it, right?

So that's the beauty.

We can still disconnect.

And the beauty here is just we're less chatty.

There is no chattiness.

Right.

Of course, the next response, if you want another topic, you do the same thing.

You can still call it a push — with Kafka it did not work for them.

They did not like the push model because let's say you have a consumer and you connect it to a topic and every time the topic has data, it pushes the data into the client's throat, literally, right?

The consumer sometimes cannot handle the volume of messages.

Right.

And that's the RabbitMQ push model.

With Kafka, it's just long polling.

Hey, let the client poll at their leisure.

Right.

So we're going to use long polling to avoid the chattiness.

And the client effectively controls the data flow.

And there is some variation that has timeouts too.

Like you cannot just poll forever.

You know, you can, but of course it has a timeout — the client timeout, there is a server timeout and stuff like that so that you don't wait forever.

Here's how long polling works.

You send a request, you get a request ID and then you ask if it's ready.

Then you just sit there.

The server knows it's not ready.

The moment it's ready, it just sends out the response, you know?

So it's a very long request-response.

If you think about it, it's almost like you send a request and then you just wait for a response.

But the beauty here is you can disconnect, right?

Unlike the normal request-response.

Long polling pros and cons.

The pros are: less chatty and back-end friendly, right?

Because the back end doesn't like to be chatted with a lot.

Clients can disconnect — that's a beauty.

Cons: it's not really real time if you think about it.

And the reason is the moment — if you could — it is real time the moment we got a response.

But if we got a message between the time I responded to a new topic and then I got a new message, the client has to still make a request, a poll request, to check if there are messages.

In this period, new messages might have come in and you didn't really get them in time.

So if the client is delayed, it is not going to get real time messages, right?

This might not be a problem for you.

And this is where the tradeoff comes in, right?

How about we do a long polling exercise?

All right.

So I wrote this up, and guys, I didn't want to write this as I explained, because it's going to take a long time.

So I wrote it beforehand and I'll explain it.

I know some of you enjoy writing as we explain, but it's the same thing effectively.

It's just to save our own time.

So it's the same identical example as with short polling, except I wrote a function that's called `checkJobComplete` on the back end.

And this function is promise-based so that it doesn't lock — and basically we give it a job ID and it just calls itself, given the job ID, and it checks if the job is not ready, it just responds with false.

But it waits for a thousand milliseconds.

And the reason why is because if you don't wait, then the event main loop will be blocked, just checking.

Right?

Effectively.

And if it's ready, you just respond with true.

And then what do you do in the check status?

Right?

You do a while loop, `await checkJobComplete`, while it's default false, just keep looping.

If you do this right — which is a bad idea, right, right, right — Node.js will just sit there and run.

If you do this, it will never finish because that will kill the Node event main loop — synchronously checking, it will sit there and just be pinned, right?

The event main loop will never move to the next results to check the next stuff, so it'll be blocked.

So adding that extra second breathing gave it some time to breathe effectively.

And let's check this out.

Run the server.

Clean.

Curl.

Submit a job.

Got a job ID and then immediately submit the job.

The job ID — give me the job.

Look at that.

We didn't get a response.

We're just waiting here.

And if I go to the back end, I'm actually printing the progress here.

So we are at 50% right now, processing the job.

60%.

70%.

You know, 80%.

And let's go back here and wait for a few more seconds.

So every 3 seconds we get a job complete.

That's how you do long polling.

Very simple.

I wouldn't say simple, to be honest.

There's more nuance to doing this, right?

We had to do some stuff here.

And effectively, because this is a contrived example, in your back end, you might be lucky and you have like some sort of a readiness, some sort of publish-subscribe where you can effectively get a push notification immediately when you get that and then you respond right there, and then you can do tricks here.

The timeout is just one.

We effectively moved the polling from the client side to the server side, if you think about it.

Which is a local polling, but hey, it works.

All right, guys, that was long polling used by Kafka.

It's very effective.

Consider using it in your back end applications.

See you in the next lecture.
