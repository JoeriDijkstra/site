---
title: "ExLLM Session Management as Processes"
date: 2025-10-25T10:55:54+02:00
draft: false
---

When you are chatting with an AI, the session with all the existing messages need to be persisted. When using ExLLM that looks something like the following:

```
session = ExLLM.new_session(:openai, name: "Customer Support")
{:ok, {response, session}} = ExLLM.chat_with_session(session, "Hello there!")
{:ok, {response2, session}} = ExLLM.chat_with_session(session, "How are you doing?")
```

## Initial Solution

So the session needs to persisted at all times. From the AI provider we get a session ID, but this is ephemeral. Also we can't retrieve the already sent messages from the session ID, so we need to store the whole struct.

What we really want to do is store it in a process, a GenServer, so that we can reference to it.

We can then send a request to our service that will contain a session id, and return it so we can feed it back into the next request. We do this for 2 reasons:

1. When we first send a request, the session will be nil, and a new one has to be created
2. When the process crashes, or the pod is restarted, we get a new session.

For the 2nd point, we also want to store all the messages in the database so that we can show the history when context runs out and feed the context back into the session when the genserver crashes.

We need a [Genserver](https://hexdocs.pm/elixir/1.19.1/GenServer.html) and not an [Agent](https://hexdocs.pm/elixir/1.12.3/Agent.html) because we also want to set a timeout on the genserver that it will destroy the process after some time of inactivity, I chose 2 hours.

And now this will work great, as long as you have 1 replica of your service running.

## Problem with Kubernetes

When you have multiple replicas it will be a little hard, because it is not certain where your request comes in. So we don't know for sure the genserver containing the data exists on that pod.

So what we need, is a global [DynamicSupervisor](https://hexdocs.pm/elixir/DynamicSupervisor.html). We can use [Horde](https://hexdocs.pm/horde/readme.html) for this. Start the processes for the session via the [Horde Dynamic Supervisor](https://hexdocs.pm/horde/Horde.DynamicSupervisor.html) and now it will work no matter how many replicas you have.

## ExLLM

ExLLM is a great package that is, at the time of writing, in limbo a little bit. `Version 1.0.0-rc1` has just come out but is not on HexPM yet, so I [forked the repo](https://github.com/bettyblocks/ex_llm) and made some fixes. Feel free to use it.
