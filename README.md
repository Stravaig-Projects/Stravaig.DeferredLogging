# Stravaig Deferred Logging

This is a logging extension that allows you to defer the output of the logs to the underlying provider until explicitly requested of a specific event happens (such as needing to log an exception).

## Why defer logs

Often times you will want diagnostics leading up to a failure in the application, however when everything is working normally this is just noise, and has cost implications on the storage of such logs, especially if you use external logging hosts. If you can defer the log output to only happen in scenarios where a failure occurs then you can have the application logs leading to the failure without the noise generated by having to search through logs when a failure did not happen.

When a failure happens you can then decide to release the logs to the provider and have the diagnostic information to hand. If the application works as normal you can then discard the logs.

## How does this work?

It works by having the `IDeferredLogger` be generated within a specific scope. In ASP.NET Core applications the scope will be the lifetime of the request. Other applications may have their own scopes.

When the scope is ended the concrete implementation will get rid of any logs it has acquired but not forwarded to a logging provider.

In the event that the logs are to be forwarded, they will be discarded once they underlying provider has received them.

## Options

When setting up the deferred logger, there are a number of options available.

You can set it up in the dependency injection container like this:

```csharp
services.AddDeferredLogging(opts =>
  {
    opts.ForwardOnException = false; // must explicitly call .ForwardLogs()
    opts.MaximumRetainedLogs = 500; // Only retain the 500 most recent logs.
  });
```

### Forward on Exception

`option.ForwardOnException = true`

This is `true` by default. When an exception is logged any deferred logs are output.

### Maximum Retained Logs

`option.MaximumRetainedLogs = 1000`

In the event that a lot of logging happens prior to the logs being released to the underlying provider, only a certain number will be retained in memory. The default is 1000. If the `MaximumRetainedLogs` are exceeded an addtional log entry will be output to indicate how many missing log entries there are. Oldest log entries are discarded first. i.e. FIFO

---

## Contributing / Getting Started

* Ensure you have PowerShell 7.1.x or higher installed
* At a PowerShell prompt
    * Navigate to the root of this repository
    * Run `./Install-GitHooks.ps1`
