---
title: "Compressing images in Elixir with Mogrify"
date: 2024-02-14T19:57:24+01:00
draft: false
---

Images can be tough to manage, and should often be uploaded unto expensive servers like AWS or Minio. This works great but you really don't want a 2mb image for a small 10px wide thumbnail. To drastically reduce image size you can use the Mogrify package, but it can be a little tricky to set up. Let's do it together.

# Installing the dependency
Add the following to your `mix.exs` file:
```elixir
{:mogrify, "~> 0.9.3"}
```
Then make sure you have imagemagick installed, since the package depends on it, this can be done by executing the following command:
```bash
sudo apt get install imagemagick
```
If you want to install it into a docker image you plan on deploying on kubernetes for instance, you can always add the following to your docker file:

```yaml
RUN apt-get update -y && apt-get install -y imagemagick
```

# Using the dependency
With that out of the way, I'll show you how I implemented it into my `Uploader.ex` module which sends the file to an S3 compatible asset store: 
```elixir
defmodule Uploader do
  @moduledoc false

  import Mogrify

  def do_upload(image_path, s3_path, convert_function \\ &compress/1) do
    converted_image = convert_function.(image_path)
    image = File.read!(converted_image.path)

    "your_bucket"
    |> ExAws.S3.put_object(s3_path, image, acl: :public_read)
    |> ExAws.request()

    {:postpone, "https://your_bucket.ams3.digitaloceanspaces.com/#{s3_path}"}
  end

  def to_thumbnail(image_path) do
    open(image_path)
    |> resize_to_limit("256x256")
    |> save
  end

  def compress(image_path) do
    open(image_path)
    |> resize_to_limit("1024x768")
    |> save
  end
end
```

This uploader takes the image, converts it into a smaller limit, dependent on what needs to happen. I have some default compression on all images, and special compression to thumbnails, which can be much smaller. This has saved me about 95% storage space on a 2mb pexels image, resulting in only a 100kb file. The nice thing about `Mogrify` is the flexibility that comes with it, this fills up an image until either axis hits the desired target, but keeps the ratio. Perfect for a social media for example. 

# Further reading:
- [Phoenix with Digital Ocean spaces]({{< ref "/posts/phoenix-with-do-spaces" >}} "Phoenix with Digital Ocean spaces")
- https://hexdocs.pm/mogrify/
- https://imagemagick.org/index.php