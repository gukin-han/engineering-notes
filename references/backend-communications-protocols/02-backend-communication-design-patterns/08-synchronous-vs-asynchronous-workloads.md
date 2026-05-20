---
date: 2026-05-20
tags: [backend, communication, sync, async]
---

# 08. Synchronous vs Asynchronous Workloads

Perhaps in my entire career, synchronous versus asynchronous workloads is the most common thing that keeps showing up when it comes to execution in backend programming, and even client side.

It's very critical to understand, especially as a backend engineer, what is synchronous and what is asynchronous execution.

It all boils down to: can I do work while waiting for whatever I just did?

If I send a request, if I send a call, if I call a method, can I do other stuff as a process while I'm waiting for you?

Because if you send a request to process something, can I do something else by design?

When we first built computing, everything was synchronous.

That means, let me go full.

Synchronous execution is like, think of it like a sine wave, right?

If two sine waves go together in the same phase, that means they are in sync.

They are going in the same direction.

So the request and the response, or the client and the server, are in sync that they are going in the same rhythm, so to speak, right?

Asynchronous means they are not in sync, basically.

That's what I mean.

Right.

And then we took this and we kind of changed this concept in programming to make it very ubiquitous.

It's everywhere when it comes to programming.

So just think of this.

This is where it came from, asynchronous motors, right?

Where the clock speeds are not really in perfect rhythm, but most of the time you're going to find out that asynchronous execution is actually preferable to synchronous, because when I do that, I don't want the server and the client to be in sync.

Who cares, right?

It's not like we have a problem with phasing or electrical stuff to keep things in sync.

That would be a disaster, right?

But in programming, we actually want things to be asynchronous.

I want the client to go do something else, and the server to do something completely different.

They don't have to be in sync.

That's the goal here.

Right.

So let's start with synchronous I/O.

Synchronous I/O: the caller sends a request and blocks.

This is the old, old, old ways.

If I'm in a process, I'm a caller and I send a request.

Right.

The request blocks me as a caller. I cannot do anything else.

The code below me cannot execute.

If there is a timer in the background, it cannot do anything.

If there is another thing that needs to be waking up events.

If the user clicks on a button — remember the nineties, if some of you know like when you built a VB5 application and you had a loop that does something, and everything was single threaded back then, when you click a button while this process is executing, the button doesn't even respond to you, right?

Because the active thing to light up the button, to actually execute it, cannot work.

That code cannot execute because we're doing something, we're blocked, we did something and we're blocked, and we have to wait for that thing.

Even if it was reading from a file, if the file is like three gigs, you cannot do anything else.

That's the original, original, original.

Now it's not the case.

Right.

That's why in VB5, I don't know if you guys remember, it's like this is my first language, that's why I use it.

We used to have a method called DoEvents.

DoEvents will make things asynchronous.

Even if there is a blocking operation, it will allow the UI to actually be responsive.

So yeah, synchronous I/O: request and block, and the process basically is blocked.

It cannot do anything.

That means if this happened in the CPU, if the process is being executed in the CPU — because the process is what? It's a set of instructions.

Right.

And the moment the process sends I/O to read, for example, from disk or from the network — again, old days.

Right.

What happens is the CPU will say, hey, you're not doing anything, you're blocked, so I'm going to take you out of the processor.

Right.

You're not executing anything.

There are no instructions for you to execute.

I'm going to take another process that has something to execute, and that's called context switching, where you were sitting in the CPU and the CPU was giving you generous execution time, but the moment you do an I/O, it kicks you out.

Sorry.

Synchronous I/O gets you kicked out.

So the caller cannot execute any code.

Meanwhile, the receiver actually responds.

Once the receiver — notice that I said caller and receiver, I don't want to say client and server because asynchronous workload is everywhere.

It doesn't have to be a client and a server.

They are disconnected.

You can have asynchronous calls in your operating system right now.

It happens all the time in your program.

There is synchronous execution that once the receiver responds, the caller will be unblocked and the operating system can put your process back to execute the rest of your code.

And this is where the caller and receiver are in complete harmony.

They are in sync effectively, right?

Because if you think of your program as a list of instructions, and the pointer — there's a pointer pointing to the server, the receiver side, and there is a pointer pointing to the caller side — they always just execute, tick, tick, tick, tick.

They are in sync.

Right?

But if it's asynchronous, we're going to notice that the client can move on while the server is actually doing other things.

They are asynchronous.

But don't worry about definitions and all that. Remember this.

This is just for example's sake.

Right?

So an example of an OS synchronous I/O.

Let's take an example.

So the program asks the OS to read from disk, and the program is taken off the CPU.

Actually, we talked about this.

Right?

The program's main thread — hey, you're reading from disk. Well, if you're reading from disk, I'm going to make the call to read from disk, but I will take you out of the CPU because that's the only thing you're going to do.

Meanwhile, as a kernel, I'm going to move on and put other processes in the CPU that are more important than you, because you're not executing anything.

You're blocked. Again, synchronous.

Are you talking about synchronous here?

Read completes and the program can resume execution.

The kernel can put the program back to the CPU.

It depends if there are other threads executing in the CPU — you guys get to wait.

And so the context switching — we're switching context from the threads and from the CPU and then putting it back.

That's costly.

When I say costly, I'm talking microseconds, of course, but if you do it a lot, it adds up.

So here is a Node.js example of synchronous I/O where we have "do work here."

It's just a bad example because Node.js is an asynchronous thing, but let's assume this is actually a blocking code, right?

If I do some work here, let's say I'm going to use the program's CPU, execute something and then I'm done, I'm going to move the execution down and then we'll go read the file.

The file reading technically is not me doing it as a program; it is a request to the kernel, which then the kernel talks to the driver, which talks eventually to the disk controller, whether it's an SSD or hard drive.

And then it does the execution.

So it sends the request to the device, and the device does the reading, and the kernel just waits there for the response from the device, from the SSD.

So neither the kernel is doing any work nor actually the application is doing any work here.

So that's a complete waste of time.

Right.

Why do you block me here?

Why did you take me off the CPU?

I'm just waiting.

I'm not doing anything, right.

And that's where we try to solve it with asynchronous execution here.

And then once the program resumes, once that is done, program resumes, we get the results.

We do something with the read file and then we continue.

Let's talk about asynchronous input output, asynchronous I/O.

The caller sends a request, and the caller can do work until it gets a response.

Here is the beauty of asynchronous I/O.

There are two choices.

It sends a request and just moves on.

But the question becomes: how does it know that it got back a response or not?

There are two ways the caller can check if the response is ready.

And this is very popular in Linux, called epoll.

Right.

There is poll and select, poll and epoll.

Right.

Which says, hey, is I/O ready? I/O ready, I/O ready?

And then they changed it to epoll, which says, hey, here's a bunch of devices that are just file descriptors, and says, tell me when it's ready.

And so both of these cases are: check if things are ready for me to actually perform a read, but that is non-blocking, right?

And the other way is the receiver calls back when it's done, and that's where either Windows, which is the I/O Completion Ports — I believe IOCP — or the new thing in Linux called io_uring, which is: hey, when it's done, we're going to write it right here.

This is the completion queue.

You're going to know if things are done. It's right here.

So two ways to do it.

To find out if your asynchronous work is done.

And Node.js — since most of you guys use Node.js — actually Node.js uses epoll in case of Linux, and in case of Windows, if you're running on Windows, it uses the completion ports.

Another trick that Node.js does is when it can't do a poll or can't do the completion stack, right?

It actually lets someone else be blocked while itself is not blocked.

Right.

That's a trick.

If you think about it, it's like, if I can make asynchronous I/O as a main thread — you asked me to read a file.

I know reading a file is a blocking operation, so I'm going to let someone else do it.

Who? I'm going to spin up a thread.

I'm going to spin up a thread and let that thread read the file so I'm free to move on.

So this is a trick that seems to the consumer, which is you, the programmer, as an asynchronous I/O because the main thread is unblocked. It just spins up a thread.

Let the thread do the reading, and the thread is now blocked, which basically means the thread asks the operating system, and the operating system asks the device controller, and the controller is now doing the work.

But the OS took the thread out of the execution, while the main thread remains in the CPU.

See.

Software is full of tricks.

Computers are dumb, and we play tricks on them.

So that's another way to do asynchronous I/O.

As long as I can move on while waiting.

That's the trick here, remember?

And it applies to everything.

By the way, the caller and receiver are not necessarily in sync.

That's the code in the code.

Why it's called asynchronous.

They are not in sync.

Who cares?

We don't want them to be in sync.

So here's an example of an asynchronous call in Node.js.

So the program spins up a secondary thread.

The secondary thread reads from disk.

Of course the OS blocks it — it being the secondary thread. The main thread is good, right?

So I can still continue doing my events if there is a UI.

I can respond to the UI.

If someone sends a request, I can pull that or get requests from Express, do stuff while I'm waiting, and only the secondary thread is being blocked.

So in Node.js — now this is Node.js speak — Node.js by default spins up, I believe, four worker threads in the libuv library that they use, and I think it's configurable of course.

Don't exceed the number of CPUs because that's pointless, right?

Because the maximum number of threads has to work on the CPU. The more you have, you can play tricks with that.

That's a different kind of topic altogether.

You know, if you know that you're I/O bound, not CPU bound, you can bump up the number of threads.

But in general, just understanding that it comes back to understanding your guts, right?

Understanding what the backend is doing — you as an engineer can configure the heck out of it, and you can optimize and squeeze all the performance.

If you don't understand how things work, you can't optimize anything.

You just won't be able to know what's happening.

"Oh my, this is slow. I have no idea why" — we don't want that.

We want to pull the curtain and understand everything.

That's our goal.

So the main program is still running and executing code, all that.

Of course, the secondary thread finished reading and calls back the main thread.

That's how it does, right?

Hey, I'm done, main thread.

You can execute the result, and then it calls back.

If you think about it, code calls back the caller. How is this done?

This is the old way.

It's still tried and true, and this is actually how Node.js works right now.

Right?

So `doWork()` — execute here `readFile`.

Right.

We're going to execute readFile, read this large void data, and then when I'm done, call this `onReadFinished` with the file, the whole file binary.

Right.

And then the moment I execute this, it's done.

I'm moving on.

The file is not done reading. The file is not done.

What happened here is we spun up a new thread.

Technically, the thread was there already.

Now we just spin them up to make them hot.

Right?

And then it was scheduled. Go read, thread, go read it.

And when you're done, please call back this function.

So if you have a function, immediately this function will get called out of the blue.

So someone just called it — "I'm done" — and then we move on.

We did "doWork", finished, right?

And then later in time, `onReadFinish` — I can think of — we can see this only `onReadFinish` will be executed.

Cool stuff.

The new approach is to do promises, where it's very similar to callbacks, but `readFile`, if you do it as a promise, you can basically immediately return a promise.

A promise in Node.js is very similar to futures in C++, I think.

So, hey, here's a future promise that, hey, take that and whatever you call `.then` on it, I will call that `.then` function.

It's a callback, but it's just nicer looking here.

And then the newer approach is to do async/await, which is still asynchronous.

Right.

But the good thing about async/await is it will look synchronous to you, so `await` will actually block you from executing the rest of the code in the vicinity, but the main thread is not technically blocked.

You know, it can do other stuff here.

So here's some Node.js lesson for you guys.

So async/await.

Right.

It's just it's awaiting.

It's not really blocking you, because sometimes you want the code to be in order.

Right.

And if you do it asynchronously, it isn't there.

It's like the rest of the code depends on this value.

Right.

So you want it to go and block, right?

Wait here until we get the result.

But we're not really waiting.

We're not blocked.

We can do other stuff.

We just — this style of programming shows us as if it's blocked.

It's just a preference, just syntactic sugar, nothing else.

All right.

We're just playing games here.

So synchronous versus asynchronous in request/response.

Let's take it to the next level.

Synchronicity, if you think about it, is really a client property.

So we talked about synchronous versus asynchronous in method calls.

But let's take it to the request/response where we have separate entities, the server and client.

Synchronicity is almost always a client property if you think about it.

Can I wait?

Can I continue working while I wait?

So most clients today — almost no client library since the network call is synchronous. We don't have synchronous anymore.

It's always asynchronous because we know we send a lot of requests and network calls and we want to do other stuff while we send the request.

And that's where async/await, where fetch, Axios — all of these libraries are all asynchronous now, so the client sends a request and continues doing work.

Right?

So you can send a request, execute code locally, other stuff, and then it gets a response and some callback is called whenever the response is executed.

So in Node.js specifically there is an event loop, a main loop, that checks: do we have a callback?

Is there a callback?

Is there a callback?

Right.

Is there some work?

Is there a timer?

Is there this?

So it's a loop that is always running and checks all this stuff.

All right.

So maybe this is still confusing sometimes.

Like to me it was always confusing, synchronous and etc., until I found some really nice example in real life.

So actually, synchronous communication is when the caller waits for a response from the receiver and technically cannot do anything else.

Right.

That's why we define synchronous communication as such. So what does that look like?

It looks like asking someone a question in a meeting.

It's actually synchronous because it would be awkward.

Like if I say, hey, John, did you actually send that pull request?

Imagine if John doesn't reply to you.

It's awkward, right?

It's just weird.

It's like I'm talking to you right here.

You have to reply right now.

I can't just ask a question and then it's like, wait, John, where are you? Where are you?

I can't just move on.

It would be really awkward.

All right, so that's synchronous communication.

Flip it the other way, right?

Asynchronous communication, which comes down to email, really.

If I send an email, I'm not sitting there waiting for a response.

Right?

I can do other work.

Right?

So that's asynchronous communication.

So email is an example of asynchronous, where meetings — especially meetings — are synchronous.

You can also argue that chatting like in Teams or Slack is also asynchronous, right?

But sometimes if you're engaged in a quick chat with someone, sometimes they reply to you immediately, sometimes it's synchronous.

So yeah, that's another example.

I thought I'd throw it in.

I like to pull from real-life examples, but technically asynchronous workload is everywhere.

And here's where I give you some examples from my personal experience in many backend applications and operating system level stuff.

So we talked about asynchronous programming.

You're familiar with that, probably, where in the language we use a promise or async/await or a future and we can continue.

We mark the function as async and we move on.

Now this is an async function, so when I call it, my process is not technically blocked, and that's the beauty here.

So that's one way.

That's one approach.

Another approach is asynchronous backend processing, right?

So if you look at this from a backend execution, and then a client sending a request to the backend, if the request is long-running, the backend is actually executing that long-running process.

It's spending a lot of time executing that.

And the client, technically you can argue, is asynchronous, it moved on, right?

It's doing its own thing, but it's technically still waiting for a response.

Right.

There is an event that needs to be triggered because it's waiting for the backend.

So the client is asynchronous, but the whole thing, the system, is synchronous.

That is synchronous processing, right?

Because the backend still thinks that, oh, someone is actually waiting for me.

Right.

I got to finish, right.

So there is a trick. Move the lens — it's like a lens.

You're looking at the client now.

Now move the lens to the backend.

To the backend.

There is a handler in the backend that says, hey, execute that.

The backend is synchronous because, hey, whoever called this is waiting for a response.

So what you can do is flip the equation: immediately respond.

What do we do?

How do we do that?

Right.

The most popular thing is called queues, this beautiful data structure.

If the client sends a request, we will not promise to execute that request immediately.

Instead, we're going to put that request into a queue.

Flush it into a queue.

Why?

Because the backend can't promise to actually process it right now; it might be processing other requests.

So you put it in the queue, and another request, put it in the queue.

So you have a queue of requests.

Right.

And the client meanwhile is immediately unblocked.

Sorry.

So the client immediately gets a response.

Hey, I queued your request. You can even disconnect if you want.

I'm good.

Here is a handle, here is a job ID, here is a promise.

Sounds familiar, right?

That is exactly like how it works locally.

Where if I call it in Node.js and it gives you a promise back.

Right.

I can check that promise, right, if it's done or not.

In the backend execution here, you send a request, you immediately respond with a job ID.

Hey, here's your job ID, check back in a few minutes, check back.

Even that is its own world.

How do I check back on the job if it's done or not? That we're going to talk about in another lecture.

It's like there are many, many ways to do that.

Push, pull, long poll, publish/subscribe, all these beautiful things.

But that's backend processing.

This is called asynchronous processing or asynchronous backend processing because we're focusing on backend engineering here, right?

So I care about the backend here.

So that's asynchronous backend processing, and you might be familiar with that, right?

If you use queues where you send a request, it's immediately queued, and you get back the job ID. Clients can disconnect and save the job somewhere on disk.

And then if it spawns back up, it gets that job ID and it says, hey, hey, hey, backend, did you finish this?

Is this done?

Yeah, it's been done for four or five minutes right now.

Here's the response.

Tricks.

It's all tricks.

Technically, the request to check if this job is done is a request/response.

Also, the first call is also a request/response.

So we just did a request/response twice — we split them.

Sometimes we're going to merge them, sometimes we're going to split them.

It all depends on what we were trying to do.

Asynchronous backend processing.

Very, very, very popular mode of operation.

I know a lot of light bulbs are going on in your heads because this course was designed, of course, for intermediate to advanced levels.

So I expected you to build some few applications before taking this course.

So you can relate to these things.

If you're new to programming generally, it might be a little bit difficult to comprehend some of this thing, but ask questions.

That's what all my Udemy courses are.

I try to answer the questions within a day or two because most of the questions are actually very, very good questions.

Right.

No questions are usually out of the blue; all are related to the course and all are useful.

So ask questions.

Don't hesitate. If you have any question, submit that question here.

So let's take it to the next level.

Postgres, one of my favorite database systems, actually has something called asynchronous commits.

Whoa.

This is a fancy little word here.

We know what a commit is, guys. Let's talk about commits here.

When I have a transaction, I begin a transaction and I write, update this table, insert another table, update another table, read from this, reading from this.

And then finally, all the changes that I made are actually being recorded in something called the WAL, or the write-ahead log.

It's a journal, a journal of changes, and that's it.

There is another data structure called the pages, where the actual results are actually living with the full columns and everything.

And that's a different data structure.

So we have the WAL, small, tiny, usually compressed, and we have the pages which have the actual page and all the columns and the rows and all that stuff.

Right?

So Postgres, when you actually write, it writes to both — writes to the page, and it also writes to the WAL.

Right.

But it only writes all of this stuff in memory, up until you say commit.

Usually when we say commit, it's enough to commit the WAL.

Even if the pages are in memory, it's enough to commit and flush the things in the WAL, because that's the changes — these tiny changes. Even if in case of a crash, we can recover from the WAL, and we can just read the WAL: okay, what was the last good state?

It was these pages.

Read them in memory, reapply all the changes from the WAL to the pages, flush them.

You are in a consistent state. You recovered. Durability.

That's one of the ACID properties.

Check out my database course if you're interested to know more.

So this is a commit.

The goal of the commit here is, in Postgres, you unblock the caller who calls commit, because the client will say, hey, I'm ready to commit.

Boom, commit.

And when you say commit, Postgres will call this commit asynchronously by default. Has to be simpler.

So the client blocks, right.

Of course the client can do other stuff meanwhile, but the client to Postgres is being blocked.

Right.

So synchronously, Postgres will only unblock and return the result — hey, success or failure — if the WAL has been flushed to disk, not to memory, to disk, has to go directly to the disk.

And the database actually skips the operating system cache.

It just goes directly to the disk, I'm sorry.

And then writes the disk.

And then once we write to the disk, whether it's a hard drive or SSD, then we return success.

That's costly because the WAL can be large.

So that's called synchronous commit. Asynchronous commits in Postgres will say this: because of transactions, if you have a lot of small transactions, let's say you don't have transactions, let's say you just insert immediately or update immediately.

Technically there is no such thing as just insert. In the backend, the database always starts a transaction for you, always, even if you didn't specify one.

Right?

So technically, if you do an insert without a transaction, we begin, right, and then commit.

That's how it works.

Autocommit effectively is on.

So in Postgres asynchronous commits, once the transaction is logically completed, all the structures updated in memory and then we build the WAL.

Right?

We will attempt to write the WAL, but immediately we're going to return success before we wait.

We don't wait for the write operation to disk to actually succeed.

We just write it and then hope it will succeed.

And we return commit immediately.

So the moment you say this — oh, this is dangerous, of course, the transaction — we can return success to the client and say, hey, success.

But what will happen on the backend: the write might fail to disk later, or maybe the server crashed and the WAL was in memory.

We lost it.

And in that case whoever reads the committed stuff will be reading dirty reads.

This turned into a database course now with all the stuff, but it's asynchronous.

Get it? Because we immediately committed, returned, and then the focus moved on to do other things.

The client moved on to do other things. Asynchronous commit.

Asynchronous I/O on Linux.

We talked about this a little bit.

So there's epoll, there is io_uring as well, where if I want to execute a read or a socket read, for example from the network, I can give the file descriptor representing my socket that I listen to. It says, hey, is there something to read?

Is there something to read?

Is there something to read?

Or I can just say I want to execute a read, but I might be blocked because the moment, if you call a read on a file descriptor that is a socket, say I want to read from this network, maybe someone just sent me something. If you call read by default without anything, it will be blocking.

You will be blocked.

So if you do it asynchronously, you flag the socket as asynchronous, then what you're going to do is just ask the socket.

Hey, tell me if this socket actually has something to read.

Right.

So this is called epoll, right?

So say whenever these file descriptors — there's a bunch of them — they are ready.

Tell me whenever they are ready. That is different than is actually complete; it is not completed.

It will tell you that there is something waiting for you in the kernel to be read.

Right.

So when you read, actually it's going to take a few microseconds to move it from the OS down to your application.

But that's very fast.

You can argue that this is also synchronous, but technically, I'd rather do it faster than actually being blocked until someone sends me data.

So that's epoll.

Of course, epoll doesn't work with files.

That's why io_uring was invented.

But this is not an operating system course, of course.

This is another complete course by itself to understand all this thing. io_uring actually works on completion instead of readiness.

That makes sense.

The OS will do the reading for you in this particular case.

Asynchronous replication, that's another one.

Replication is the idea of having a primary writer in the database and a lot of secondary worker databases that are performed for read.

So if I write to my primary database, I say I begin a transaction and I write, write, write, write, insert, insert, insert, update, update.

These updates will be streamed to all the replicas, as I'm writing.

So the replicas will receive these changes, but then the user says, hey, commit. If I say commit, there are two ways to do it.

There is the synchronous way, the synchronous replication commit, where synchronous replication is: if you issue a commit, the main transaction in the primary doesn't actually commit.

It will send commits to all the secondaries.

Hey, commit, commit, commit, commit, commit — and this is where two-phase commit comes in, and three-phase commit, and you know, Paxos and all that stuff.

Right.

Saying hey, commit, commit, commit, commit, commit, commit.

And only if all of them or the majority have committed, then I'm going to commit.

Get it?

So the client actually is blocked while it waits. That's synchronous. It's blocked, the client who says, hey, commit. This replication work — it's waiting. So you can do it asynchronously.

You can configure asynchronous replication at a cost of consistency, right?

Which says, hey, if I ask you to commit, you mean — that's your what matters?

Please just commit and return.

And then asynchronously in the backend, just issue your commands and make sure everyone actually gets the data.

But I don't care about them.

I care about you.

I care about you committing.

Give me the result.

So and even in that, you can also enable asynchronous commits.

In that instance, different things.

So asynchronous commit at the WAL level and asynchronous replication.

You see, asynchronous work is everywhere, guys.

And finally, asynchronous OS sync.

So the database people, actually — I don't know if you know this, guys — but when you write a file to your operating system, it doesn't actually go to disk immediately.

It stays in something called the OS file system cache.

So whatever the file system, whether it's NTFS or whatever, on Linux you use ext4 — whatever, it stays there. There is a cache in the OS that the write goes to, in forms of pages because we try to write in pages, and then asynchronously the operating system will wait for a lot of writes in the memory and then flush them all together.

Without this, it's going to be a problem because all writes will go to the disk and you're going to fragment your disk.

There's going to be a lot of chattiness on the disk because all the writes — let's say you're going to change one byte, right? You'll write one byte in page one, in this file, and then you're going to save another byte and then save another byte.

This will be translated into three writes to disk, and on the SSD, you destroy that. That is really bad. You try not to — there is a shelf life for each page, the erasable unit on the SSD has a shelf life, and the more you write to the same page, first of all, you have to erase it because the moment you discard it, you have to write somewhere else.

So if you write a lot to the same block on disk, eventually it dies out, and there is durability to the SSD.

That's why the OS tries to minimize the amount of writes by batching them in the cache, which is a good thing.

But database people don't like this, right?

If I want to ensure strong consistency, I write my WAL changes — you better flush them to disk the moment I tell you. Don't go through the OS cache.

No, I don't want to wait for 200 nanoseconds or 200 milliseconds.

I don't want to wait.

I want to flush.

I want to make sure it's on the disk.

Right.

That's what the database people ask for.

So there is this fsync property that you can turn on or off to skip the OS cache, and Linux — Linus Torvalds hates it, really, he doesn't like really anything about database people.

He thinks that the database people will actually ruin Linux, I think. He actually mentioned it twice because of all these workarounds and hacks.

How about actually do some demo?

Node.js blocking call with a loop and doing some fetch.

How about we do that?

All right, guys, how about we actually create a quick Node.js application that shows synchronous and asynchronous execution, where all the source code will be available for you guys?

I'm going to share it there.

So let's say `node-sync-async`, right?

Let's go create `sync.js`, and I'm going to create another one called `async.js`.

And then in the `sync.js`, what I'm going to do is `const fs = require('fs')`, let's get the fs library, and then do a `console.log(1)`, and then I'm going to now read a file.

So let's go ahead and create the file here.

There's the text. Some text here.

And now what I'm going to do is `readFileSync`.

I'm going to use the sync version of the file.

It's the synchronous version.

So let's read through this here.

Returns the content of the path.

For details on operations, see the documentation of the asynchronous version of the file read, `readFile`.

So what this does is actually does it synchronously.

Right?

And of course `readFileSync` is platform specific.

Of course it changes.

Right.

But all remains the same where it blocks effectively.

Right.

So let's go ahead and read the test text.

Read the file, and then once we're done, I suppose I can just do `console.log` of that.

Or just do `const result`, call this and then `console.log` of that.

Right.

And then `console.log(2)`.

And so what should be the result here?

Let's go ahead and do this.

All right.

So let's try to run it.

`node sync.js`.

You can see that we executed 1.

Then the file is read.

We're blocked.

The 2 wasn't executed.

Right.

And then the 2 was executed.

Very simple.

And the reason is because we were blocked right here.

We're physically actually blocked reading this file because it's synchronous.

The main event loop is actually executing this and it's blocked reading that.

So careful doing that.

Right.

So now let's do the async.

The async is actually just doing `readFile`.

And when it's done, I want you to go and `console.log`.

I think that works too, because that will pass in the whole object.

So we're going to get more than the file, but a very similar but asynchronous execution.

So the file will be read whenever it's ready, and the `console.log` function will be called and pass in the file.

Let's see what was the result here.

In this case, Node is saying, oh geez, let's zoom in here and run, and you can see that 1, 2, and then the actual buffer here, we read the whole file basically.

Oh, yeah, we're going to read the description next time.

So the callback looks like the error and then the data, right?

So you have to do it this way, `(err, data)`.

And then we care about the data.

The reason we got null is because basically there was no error.

So let's do it again.

There you go.

We got Buffer.

I suppose we have to kind of decode this.

Let's do `data.toString()`.

Here you go.

We had to convert. By default, we get arrays, the actual buffer object.

We just need to take the buffer object and take it there.

But the execution is what matters.

1, 2, and lower, right?

So 1 was executed.

This was scheduled. 2 was executed.

And then this function was called.

That's basically the difference.

So that's basically the difference in async versus synchronous execution.

Of course, we can take this to the next level.

It's everywhere.

Asynchronous execution is everywhere.

See you in the next lecture, guys.
