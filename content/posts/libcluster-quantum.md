---
title: "Starting Quantum job from multiple k8s nodes"
date: 2025-02-15T12:57:10+01:00
draft: false
---

Quantum is an amazing elixir package for starting scheduled jobs in a genserver. I would recommend checking it out [here](https://github.com/h4cc/quantum-elixir), it has been a great help in many projects.

## The issue
There is one problem though, and that is when using multiple pods of a service running quantum, executing the jobs multiple times as well. This is something you would probably want to prevent. The global mode has been deprecated in version 3.0.0, with the reason being:

> (...) the implementation wasn’t great and was causing us a lot of trouble with unexpected behavior.

## The solution
So how would you fix this? Well the way we have decided to do it is to use libcluster as well. So start by adding Quantum and Libcluster to you `mix.exs`:
```
{:quantum, "~> 3.0"},
{:libcluster, "~> 3.0"}
```

## Setting up libcluster
Now what libcluster allows you to do is manage your Kubernetes nodes from Erlang. Essentially what we are going to do is register a headless template which will help k8s to find your pods by using DNS lookups. A config might look like this:
```
# Clustering config
if is_binary(System.get_env("KUBERNETES_SERVICE_HOST")) do
  config :your_app,
    libcluster: [
      kubernetes: [
        strategy: Elixir.Cluster.Strategy.Kubernetes.DNS,
        config: [
          service: "your-app-headless",
          application_name: "your-app",
          polling_interval: 10_000
        ]
      ]
    ]
end
```
where we can see we use a headless service again to discover all our pods through our DNS server. This will help us when we register our supervisor. Create a new supervisor module called SchedulerSupervisor and add the following to it:
```
@moduledoc """
Responsible for starting the scheduled job on a single node
"""

use Supervisor

def start_link(quantum, opts) do
    case :global.whereis_name(__MODULE__) do
      :undefined ->
        with {:error, {:already_started, pid}} <- do_start_link(quantum, opts) do
          Process.link(pid)
          {:ok, pid}
        end

      pid ->
        Process.link(pid)
        {:ok, pid}
    end
  end

  defp do_start_link(quantum, opts) do
    Supervisor.start_link(__MODULE__, {quantum, opts}, name: {:global, __MODULE__})
  end

  def init(state) do
    :global.re_register_name(__MODULE__, self(), &resolve_global_conflict/3)
    Quantum.Supervisor.init(state)
  end

  defp resolve_global_conflict(_name, pid_to_keep, pid_to_kill) do
    Supervisor.stop(pid_to_kill)
    pid_to_keep
  end
```
this will ensure our supervisor is started only once across all zones. When we init we use the `re_register_name` erlang function ([Documentation](https://www.erlang.org/docs/21/man/global#re_register_name-3)) and we pass in a function to resolve when it is already registered. This function is our `resolve_global_conflict` function which kills the duplicate supervisor and keeps the current one alive, and passes the process id back so we can use and link it. 


## Integrating Quantum
The last thing we need to do is add Quantum into the `mix` (get it?) by creating a new scheduler module. This could look something like this:
```
defmodule Scheduler do
  @moduledoc false
  // the supervisor module is the module we created above
  use Quantum, otp_app: :your_app, supervisor_module: SchedulerSupervisor 
end
```
as you can see here we can define a supervisor_module option in our macro which will be used by the `start_link` function for the Quantum job, usually this is a default quantum supervisor. This can also be passed in the `application.ex` of your app but this is a much neater solution in my opinion. 

## Adding it to the application on start
And let's not forget to add it to the `application.ex` under your children:

```
@impl true
def start(_type, _args) do
  children = supervisor_libcluster() ++ [Scheduler]

  opts = [strategy: :one_for_one]
  Supervisor.start_link(children, opts)
end

defp supervisor_libcluster do
  config = Application.get_env(:your_app, :libcluster)

  if config do
    [{Cluster.Supervisor, [config, [name: ClusterSupervisor]]}]
  else
    []
  end
end
```

And there we have it! Now the job only runs once even tough we have multiple pods running!

## Further reading:
- https://hexdocs.pm/libcluster/Cluster.Strategy.Kubernetes.DNS.html
- https://elixirforum.com/t/running-quantum-job-from-single-node/33611
- https://hexdocs.pm/quantum/run-strategies.html
- https://github.com/h4cc/quantum-elixir
- https://github.com/bitwalker/libcluster