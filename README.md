[BetterParams][docs]
====================

[![Build Status][shield-travis]][travis-ci]
[![Coverage Status][shield-inch]][docs]
[![Version][shield-version]][hexpm]
[![License][shield-license]][hexpm]

> Cleaner request parameters in Elixir web applications 🙌

`BetterParams` is a simple Elixir [Plug][plug] that allows passed
request parameters to be called as `atoms` instead of Strings. The
sole purpose of this Plug is to pattern match on maps with atom keys
instead of string keys in routers/controllers to calm my OCD down.

<br>




## Usage

Once installed, it lets you pattern match request parameters like
`%{id: id}` instead of `%{"id" => id}` in Phoenix applications:

```elixir
# web/controllers/some_controller.ex

def show(conn, %{id: id}) do
  # do something
end

def create(conn, %{id: id, post: %{title: title, body: body}}) do
  # do something
end
```


#### Notes

 - Implementation uses [`String.to_existing_atom`][string-atom] to prevent
   against [DoS attacks][gh-issue-ddos], so it only converts those params
   to atoms that you use in your application.
 - You can continue to use String keys without breaking your existing
   matches if you want. All request parameters are available for both
   `String` and `Atom` keys (that have already been defined in the
   application).
 - This doesn't pollute your Request Logs with duplicate params.
 - For other `Plug.Router` based applications, you can also access request
   params similarly by calling them like `conn.params[:id]` or
  `conn.params.post.title`.

<br>




## Installation

Add `better_params` to your project dependencies in `mix.exs`:

```elixir
def deps do
  [{:better_params, github: "dangyuzhang/better_params"}]
end
```



### Phoenix Framework

For Phoenix applications, call the plug at the end of the `controller`
method in `web/web.ex` (inside the `quote` block):

```elixir
# web/web.ex

def controller do
  quote do
    use Phoenix.Controller

    # Other stuff...

    plug BetterParams
  end
end
```

Alternatively, you can also call it your Router Pipelines or in
individual controllers directly.



### Other Plug.Router Apps

For other applications using `Plug.Router`, call the Plug anytime after
calling the `:match` and `:dispatch` plugs:

```elixir
defmodule MyApp.Router do
  use Plug.Router

  plug :match
  plug :dispatch
  plug BetterParams

  # Rest of the router...
end

```



### Removing String Keys Entirely

If your use case calls for a params object with _only_ `Atom` keys, you
may pass the option `drop_string_keys` to the plug. Much as it says on
the can, this will replace the `String`-type keys altogether, rather
than preserving them alongside the `Atom` keys.

```elixir
plug BetterParams, drop_string_keys: true
```

<br>




## Roadmap

 - [x] Write Tests
 - [x] Write Documentation
 - [x] Symbolize the collective `params` map
 - [x] Option to remove string keys entirely
 - [ ] Symbolize [individual parameter maps][plug-params] (if the need arises)
    - [ ] `path_params`
    - [ ] `body_params`
    - [ ] `query_params`

<br>




## Contributing

 - [Fork][github-fork], Enhance, Send PR
 - Lock issues with any bugs or feature requests
 - Implement something from Roadmap
 - Spread the word :heart:

<br>




## License

This package is available as open source under the terms of the [MIT License][license].

<br>




  [shield-version]:   https://img.shields.io/hexpm/v/better_params.svg
  [shield-license]:   https://img.shields.io/hexpm/l/better_params.svg
  [shield-downloads]: https://img.shields.io/hexpm/dt/better_params.svg
  [shield-travis]:    https://img.shields.io/travis/sheharyarn/better_params/master.svg
  [shield-inch]:      https://inch-ci.org/github/sheharyarn/better_params.svg?branch=master

  [travis-ci]:        https://travis-ci.org/sheharyarn/better_params
  [inch-ci]:          https://inch-ci.org/github/sheharyarn/better_params

  [license]:          https://opensource.org/licenses/MIT
  [hexpm]:            https://hex.pm/packages/better_params
  [plug]:             https://github.com/elixir-lang/plug
  [plug-params]:      https://hexdocs.pm/plug/Plug.Conn.html#module-fetchable-fields
  [string-atom]:      https://hexdocs.pm/elixir/String.html#to_existing_atom/1

  [docs]:             https://hexdocs.pm/better_params

  [github-fork]:      https://github.com/sheharyarn/better_params/fork
  [gh-issue-ddos]:    https://github.com/sheharyarn/better_params/issues/1
