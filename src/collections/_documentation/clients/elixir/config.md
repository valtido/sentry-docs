---
title: Configuration
---

Configuration is handled using the standard Elixir configuration.

&nbsp;
### Using `config/prod.exs`
Simply add configuration to the `:sentry` key in the file `config/prod.exs`:

```elixir
config :sentry,
  dsn: "___PUBLIC_DSN___"
```

&nbsp;
### Using an env with Plug or Phoenix
If using an environment with Plug or Phoenix, add the following to your router:

```elixir
use Plug.ErrorHandler
use Sentry.Plug
```

&nbsp;
### Capture Errors from Separate Processes
If you’d like to capture errors from separate processes, like _Task_ that may crash, add the line `:ok = :error_logger.add_report_handler(Sentry.Logger)` to your application’s start function:

```elixir
def start(_type, _opts) do
  children = [
    supervisor(Task.Supervisor, [[name: Sentry.TaskSupervisor]]),
    :hackney_pool.child_spec(Sentry.Client.hackney_pool_name(),  [timeout: Config.hackney_timeout(), max_connections: Config.max_hackney_connections()])
  ]
  opts = [strategy: :one_for_one, name: Sentry.Supervisor]

  :ok = :error_logger.add_report_handler(Sentry.Logger)

  Supervisor.start_link(children, opts)
end
```

&nbsp;
## Required settings

`environment_name`

: The name of the environment. This defaults to the `:dev` environment variable.

&nbsp;
`dsn`

: The DSN provided by Sentry.

&nbsp;
`root_source_code_path`

: This is only required if `enable_source_code_context` is enabled. Should generally be set to `File.cwd!`.

&nbsp;
## Optional settings

`included_environments`

: The list of environments you want to send reports to Sentry. This defaults to `~w(prod test dev)a`.

&nbsp;
`tags`

: The default tags to send with each report.

&nbsp;
`release`

: The release to send to Sentry with each report. This defaults to nothing.

&nbsp;
`server_name`

: The name of the server to send with each report. This defaults to nothing.

&nbsp;
`client`

: If you need different functionality for the HTTP client, you can define your own module that implements the _Sentry.HTTPClient_ behavior and set _client_ to that module.

&nbsp;
`filter`

: If you would like to prevent certain exceptions from being sent, set this to a module that implements the `Sentry.EventFilter` behavior. See below for further documentation.

&nbsp;
`hackney_pool_max_connections`

: Number of connections for Sentry’s hackney pool. This defaults to 50.

&nbsp;
`hackney_pool_timeout`

: Timeout for Sentry’s hackney pool. This defaults to 5000 milliseconds.

&nbsp;
`hackney_opts`

: Sentry starts its own hackney pool named `:sentry_pool`, and defaults to using it. Hackney’s `pool` configuration, as well as others like proxy or response timeout, can be set through this configuration as it is passed directly to hackney when making a request.

&nbsp;
`before_send_event`

: This option allows performing operations on the event before it's sent by `Sentry.Client`. It accepts an anonymous function, or a {module, function} tuple. The event will be passed as the only argument.

&nbsp;
`after_send_event`

: This option allows performing arbitrary operations after attempting to send an event. It accepts an anonymous function, or a {module, function} tuple, and the event will be passed as the first argument. The result of sending the event will be passed as the second argument.

&nbsp;
`sample_rate`

: The sampling factor to apply to events. A value of 0.0 will deny sending any events, and a value of 1.0 will send 100% of events.

&nbsp;
`in_app_module_whitelist`

: Expects a list of modules that is used to distinguish among stacktrace frames that belong to your app, and ones that are part of libraries or core Elixir. This is used to better display the significant part of stacktraces. The logic is greedy, so if your app’s root module is `MyApp` and your setting is `[MyApp]`, that module as well as any submodules like `MyApp.Submodule` would be considered part of your app. Defaults to `[]`.

&nbsp;
`report_deps`

: Will attempt to load Mix dependencies at runtime to report alongside events. Defaults to _true_.

&nbsp;
`enable_source_code_context`

: When true, Sentry will read and store source code files to report the source code that caused an exception.

&nbsp;
`context_lines`

: The number of lines of source code before and after the line that caused the exception to be included. Defaults to `3`.

&nbsp;
`source_code_exclude_patterns`

: A list of Regex expressions used to exclude file paths that should not be stored or referenced when reporting exceptions. Defaults to `[~r"/_build/", ~r"/deps/", ~r"/priv/"]`.

&nbsp;
`source_code_path_pattern`

: A glob that is expanded to select files from the `:root_source_code_path`. Defaults to `"**/*.ex"`.

&nbsp;
## Testing Your Configuration

To ensure you’ve setup your configuration correctly, we recommend running the included Mix task. It can be tested on different Mix environments and will tell you if it's not currently configured to send events in that environment:

```bash
$ MIX_ENV=dev mix sentry.send_test_event
Client configuration:
server: https://sentry.io/
public_key: public
secret_key: secret
included_environments: [:prod]
current environment_name: :dev

:dev is not in [:prod] so no test event will be sent

$ MIX_ENV=prod mix sentry.send_test_event
Client configuration:
server: https://sentry.io/
public_key: public
secret_key: secret
included_environments: [:prod]
current environment_name: :prod

Sending test event!
```
