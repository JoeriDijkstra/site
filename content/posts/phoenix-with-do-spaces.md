---
title: "Phoenix With Digital Ocean Spaces"
date: 2024-01-28T14:02:48+01:00
draft: false
---

Using aws as object storage works pretty well for most cases, but it can be expensive and very complex to set up. That is why for personal projects I prefer using DigitalOcean's spaces. They are cheap to operate and easy to manage, and what is important for us is that it is S3 compatible.

# The goal
In this blog post I want to create a new basic file upload using the new phoenix liveview `live_file_upload`. These files will then be uploaded to the spaces and the url will be saved. Something to keep in mind is that using a bucket policy is not supported directly within Digital Ocean, but you can set a policy via `s3cmd`. This will be out of scope, as is compressing images for upload, but I might revisit that in the future.

# Setting up the environment
I assume you have your access key and secret access key. Create a file and call it `.env`, it should look like this:

```bash
#!/usr/bin/env zsh

export AWS_ACCESS_KEY_ID={YOUR ACCESS KEY ID}
export AWS_SECRET_ACCESS_KEY={YOUR SECRET ACCESS KEY}
```

We will be using ExAws for this project, so import it in your `mix.exs` file:
```elixir
{:ex_aws, "~> 2.1"},
{:ex_aws_s3, "~> 2.0"},
{:hackney, "~> 1.15"},
{:sweet_xml, "~> 0.6"},
```
Then we'll have to set our config to use these environment keys. Go to your `config.exs`, or if you use phoenix the `runtime.exs` and let's configure ExAws. Add the following to the bottom:
```elixir
config :ex_aws,
  debug_requests: true,
  json_codec: Jason,
  access_key_id: System.get_env("AWS_ACCESS_KEY_ID"),
  secret_access_key: System.get_env("AWS_SECRET_ACCESS_KEY")

config :ex_aws, :s3,
  scheme: "https://",
  host: "ams3.digitaloceanspaces.com",
  region: "ams3"
```
Since I am in amsterdam I'll use the ams3 regio, and add it to the host. You can fetch this information from the endpoint url on your Digital Ocean space. We have not yet provided a bucket since often you'll want to use multiple buckets in a project, but you could set one up here.

# Creating the phoenix app
Some prerequisites for this part, you'll have to make sure you have somewhere to store the file url, like a string on a table. I'll be using the `user` table for this, with an `avatar` field.

## The liveview
In your phoenix app, create a new liveview. We'll start with a simple render function:
```elixir
def render(assigns) do
  ~H"""
  <.simple_form for={@avatar_form} phx-submit="update_avatar" phx-change="validate_avatar">
    <%= for entry <- @uploads.avatar.entries do %>
      <.live_img_preview entry={entry} />
    <% end %>

    <.live_file_input upload={@uploads.avatar}>

    <:actions>
      <.button phx-disable-with="Changing...">Change Avatar</.button>
    </:actions>
  </.simple_form
  """
end
```
This renders a form with a file input for the users avatar. There are some interesting things at play here, first is the simple_form which takes in the avatar form we will define in the mount. Then it has a submit and a validate, the validate is important because otherwise it will not work. Furthermore there is a for loop which shows previews of the uploaded files, and finally we have the file input which takes in the uploads. If you run this it will fail because in the mount we still have to allow files to be uploaded.

## The mount function
I won't go into detail over changesets and how to store things in the database, but we need a changeset for the form, which will be our user, and we will only cast the avatar url in this changeset to update. This means it will look a little like:
```elixir
def mount(_, _, socket) do
  avatar_changeset = Accounts.change_user_avatar(user)

  socket =
    socket
    |> assign(:avatar_form, to_form(avatar_changeset))
    |> allow_upload(:avatar,
      accept: ~w(.jpg .jpeg .png),
      max_entries: 1
    )

  {:ok, socket}
end
```
Let me explain a little, we allow get the changeset, assign it to the avatar form, and allow the upload of the avatar, this will also give us access to the `@uploads` key in the assign. We set the max entries at a time to 1 and only accept a few image types.

## Validating
Validating is the easiest part, since this will just return the socket. It is still important to have this function, but you could do checks in here if necessary.
```elixir
def handle_event("validate_avatar", _, socket) do
  {:noreply, socket}
end
```

## Saving the object
Now for the fun part, saving the object and uploading it. This will be done in the `save_avatar` event. So our code will look something like this to save it:
```elixir
def handle_event("update_avatar", _, socket) do
  %{current_user: user} = socket.assigns

  case Accounts.update_user_avatar(user, %{"avatar_path" => List.first(consume_files(socket))}) do
    {:ok, _user} ->
      socket =
        socket
        |> put_flash(:info, "Succesfully updated avatar")
        |> push_navigate(to: ~p"/")

    {:noreply, socket}

    {:error, _changeset} ->
      {:noreply, put_flash(socket, :error, "Something went wrong updating your avatar")}
  end
end
```
Above you can see that we update the user avatar with the first item from the `consume_files` function. This `consume_files` option function will do the actual uploading and returns the public url, and stores it. Let's define the `consume_files` function:
```elixir
defp consume_files(socket) do
  consume_uploaded_entries(socket, :avatar, fn %{path: path}, %{uuid: uuid} ->
    image = File.read!(path)

    "your_bucket"
    |> ExAws.S3.put_object("avatars/avatar-#{uuid}.jpg", image, acl: :public_read)
    |> ExAws.request()

    {:postpone, "https://your_bucket.ams3.digitaloceanspaces.com/#{s3_path}"}
  end)
end
```
I've done the `acl: :public_read` to make sure the files are publicly available to show via the URL. The file is stored in a temp path until you consume the entry, which we'll read and upload to our space, in the given bucket. Now your image is uploaded in your Digital Ocean Space and the URL stored in your object, you can now use the image wherever you want in your application. This can be any file of course. 