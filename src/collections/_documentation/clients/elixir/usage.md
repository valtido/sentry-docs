---
title: Usage
sidebar_order: 1
---

<!-- WIZARD -->

&nbsp;
## Capturing Errors

If you use the error logger and setup Plug/Phoenix, then you're already done. All errors will bubble up to Sentry.

Otherwise, we provide a simple way to capture exceptions:

```elixir
try do
  ThisWillError.reall()
rescue
  my_exception ->
    Sentry.capture_exception(my_exception, [stacktrace: System.stacktrace(), extra: %{extra: information}])
end
```
<!-- ENDWIZARD -->

&nbsp;
## Optional Attributes

With calls to `capture_exception`, additional data can be supplied as a keyword list:

> ```elixir
> Sentry.capture_exception(ex, opts)
> ```

&nbsp;
###### **`extra`**  
[](This space helps the next line indent correctly)

: Additional context for this event. Must be a mapping. Children can be any native JSON type.

  ```elixir
  extra: %{key: "value"}
  ```

&nbsp;
###### **`level`**
[](This space helps the next line indent correctly)

: The level of the event. Defaults to `error`.

  ```elixir
  level: "warning"
  ```

  Sentry is aware of the following levels:

  -   debug (the least serious)
  -   info
  -   warning
  -   error
  -   fatal (the most serious)

&nbsp;
###### **`fingerprint`**
[](This space helps the next line indent correctly)

: The fingerprint for grouping this event.

&nbsp;
###### **`tags`**
[](This space helps the next line indent correctly)

: Tags to index with this event. Must be a mapping of strings.

  ```elixir
  tags: %{"key" => "value"}
  ```

&nbsp;
###### **`user`**
[](This space helps the next line indent correctly)

: The acting user.

  ```elixir
  user: %{
      "id" => 42,
      "email" => "clever-girl"
  }
  ```

&nbsp;
###### **`event_source`**
[](This space helps the next line indent correctly)

: The source of the event. Used by the _Sentry.EventFilter_ behavior.

&nbsp;
## Breadcrumbs

Sentry supports capturing breadcrumbs --- events that happened prior to an issue. We need to be careful because breadcrumbs are per-process. If a process dies, it might lose its context.

```elixir
Sentry.Context.add_breadcrumb(%{my: "crumb"})
```
