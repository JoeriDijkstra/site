---
title: "Parsing options in Elixir"
date: 2023-12-29T12:18:16+01:00
draft: false
---

Options are often done in a keyword list in Elixir. The problem with this is that keyword lists are order-bound. This means that pattern matching is going to be hard the first time, and it is a problem I had when creating MapGrid.

You may expect the pattern to match when you try the following:
```
defp work(async: true, should_run: true), do: async_work()
defp work(async: false, should_run: true), do: sync_work()
defp work(_), do: :ok
```

Then you try to run the command:
```
work(should_run: true, async: false)
```

But this **will not** match, since it is order dependent and you want to match on multiple options or an arbitrary amount of options it will fail to match.

Here is the way I solved it, it is not the perfect solution, and might not be the only one out there, but I think it makes for an elegant flow which is easily understandable and extendable.

```
defp work(opts) do
    opts_map = Enum.into(opts, %{})
    do_work(opts_map)
end

defp do_work(%{async: true, should_run: true}), do: async_work()
defp do_work(%{async: false, should_run: true}), do: sync_work()
```

Now when running the following code it will work:
```
work(should_run: true, async: false)
```

This is because keyword lists are order dependent, and maps are not. So when we call the Enum.into function we basically convert it into a map we can easily match on. In my opinion this is a much neater solution than having a list of if-statements to control the flow.

It is also used in the MapGrid code, with multiple optionable features which can be toggled (the options are converted into a map first):
```
  defp apply_options(item, options) do
    item
    |> apply_function(options)
    |> reduce_to_keys(options)
  end

  defp apply_function(item, %{item_function: function}), do: function.(item)
  defp apply_function(item, _), do: item

  defp reduce_to_keys(item, %{keys: keys}) do
    Enum.reduce(keys, [], &[{&1, Map.get(item, &1)} | &2])
  end

  defp reduce_to_keys(item, _), do: item
```

Here it will always return the item and I can easily add more functions like this to the pipeline in apply_options. If you think there are other better ways to handle options please let me know.
