<!--
Guideline: This page is comprehensive; select the Options specific to your SDK and delete the ones that do not apply; it is stored in the common folder, nested under _includes/common. To use, 

1. Add a folder with the name of the platform you are documenting to the _documentation/sdks structure (for example, _documentation/sdks/javascript) 
2. Create a new config-<sdk>-basics.md file in _documentation/sdks/<platform-name> 
3. Create the defined `include` statements and add them to the configuration-basics.md file

If you have questions, please ask Fiona or Daniel. 

**The objective for this page is that a developer can view easily the configuration options for the SDK; each page _must_ have a description that includes a summary of what the page provides to the developer. Simply linking the page is insufficient.**
-->
# Basic Configuration Options

The {{ include.sdk_name }} SDK is configurable using a variety of options. These options are set when the SDK is first initialized, passed to the `init()` as an object:

{{ include.config_basic_content }}

These options can be read from an environment variable or your `~/.sentryclirc` file (`SENTRY_DSN`, `SENTRY_ENVIRONMENT`, `SENTRY_RELEASE`) are read automatically.

`dsn`

The *DSN* tells the SDK where to send the events. If this value is not provided, the SDK will try to read it from the `SENTRY_DSN` environment variable. If that variable also does not exist, the SDK will not send any events.

In runtimes without a process environment (such as the browser SDK), the fallback of reading from the environment variable does not apply.

`debug`

Turns debug mode on or off. If debug is enabled, the SDK will attempt to print out useful debugging information if something goes wrong with sending the event. The default is always `false`. It’s generally not recommended to turn it on in production, though turning debug mode on will not cause any safety concerns.

`release`

Sets the release. The SDK will try to automatically configure a release out of the box if it's provided by `window.SENTRY_RELEASE`, but it's best to set it manually to guarantee that the release is in sync with your deploy integrations or source map uploads.

Release names are strings, but some formats are detected by Sentry and might be rendered differently. For more information have a look at [the releases documentation](https://docs.sentry.io/workflow/releases/).

`environment`

Sets the environment. This string is freeform and not set by default. A release can be associated with more than one environment to separate them in the Sentry web UI (for example, `staging` as compared to `prod` or similar).

By default the SDK will try to read this value from the `window` environment variable.

`error_types`

Sets which errors are reported. It takes the same values as PHP’s `[error_reporting](https://www.php.net/manual/errorfunc.configuration.php#ini.error-reporting)` configuration parameter.

By default all types of errors are be reported (equivalent to `E_ALL`).

`sampleRate`

Configures the sample rate as a percentage of events to be sent in the range of `0.0` to `1.0`. The default is `1.0` which means that 100% of events are sent. If set to `0.1` only 10% of events will be sent. Events are picked randomly.

`maxBreadcrumbs`

This variable controls the total amount of breadcrumbs that should be captured. This variable defaults to `100`.

`attachStacktrace`

When enabled, stack traces are automatically attached to all messages logged. Stack traces are always attached to exceptions; however, when this option is set, stack traces are also sent with messages. This option, for instance, means that stack traces appear next to all log messages. This feature is `off` by default.

Grouping in Sentry is different for events with stack traces and without. As a result, you will get new groups as you enable or disable this flag for certain events.

`sendDefaultPii`

If this flag is enabled, certain personally identifiable information is added by active integrations. By default no such data is sent.

If possible, we recommend that you turn this feature on to send all such data by default, and manually remove what you don’t want to send using our features for managing *[Sensitive Data](https://docs.sentry.io/data-management/sensitive-data/)*.

`serverName`

Can be used to supply a “server name.” When provided, the name of the server is sent along and persisted in the event. For many integrations the server name actually corresponds to the device hostname even in situations where the machine is not actually a server. Most SDKs will attempt to auto-discover this value.

`blacklistUrls`

A list of strings or regex patterns that match error URLs that should not be sent to Sentry. This is a "contains" match to the entire file URL. As a result, if you add `foo.com` to it, it will also match on `https://bar.com/myfile/foo.com`. By default, all errors will be sent.

`whitelistUrls`

A list of strings or regex patterns that match error URLs that should exclusively be sent to Sentry. This is a "contains" match to the entire file URL. As a result, if you add `foo.com` to it, it will also match on `[https://bar.com/myfile/foo.com](https://bar.com/myfile/foo.com)` By default, all errors will be sent.

`inAppInclude`

A list of string prefixes of module names that belong to the app. This option takes precedence over `in_app_exclude`.

`inAppExclude`

A list of string prefixes of module names that do not belong to the app, but rather third-party packages. Modules considered not to be part of the app will be hidden from stack traces by default.

This option can be overridden using `in-app-include`.

`requestBodies`

This parameter controls if integrations should capture HTTP request bodies. It can be set to one of the following values:

- `never`: request bodies are never sent.
- `small`: only small request bodies will be captured where the cutoff for small depends on the SDK (typically 4KB)
- `medium`: medium-sized requests and small requests will be captured. (typically 10KB)
- `always`: the SDK will always capture the request body for as long as sentry can make sense of it

`withLocals`

When enabled local variables are sent along with stackframes. This can have a performance and PII impact. Enabled by default on platforms where this is available.

`caCerts`

A path to an alternative CA bundle file in PEM-format.

## **Integration Configuration**

For many platform SDKs integrations can be configured alongside it. On some platforms that happen as part of the `init()` call, in some others, different patterns apply.

`integrations`

In <SDK name>, the integrations are configured through this parameter on library initialization. 

`defaultIntegrations`

This can be used to disable integrations that are added by default. When set to `false` no default integrations are added.

## **Hooks**

These options can be used to hook the SDK in various ways to customize the reporting of events.

`beforeSend`

This function is called with an SDK specific event object and can return a modified event object or nothing to skip reporting the event. Use this hook, for instance, for manual PII stripping before sending.

`beforeBreadcrumb`

This function is called with an SDK-specific breadcrumb object before the breadcrumb is added to the scope. When nothing is returned from the function, the breadcrumb is dropped. To pass the breadcrumb through, return the first argument, which contains the breadcrumb object. The callback typically gets a second argument (called a “hint”) that contains the original object from which the breadcrumb was created to further customize what the breadcrumb should look like.

## **Transport Options**

Guideline: Adopt the appropriate transport option for the SDK; modify the descriptions below to be specific to the SDK you're documenting.

Transports are used to send events to Sentry. Transports can be customized to some degree to better support highly specific deployments.

`transport`

Switches out the transport used to send events. 

In <this SDK> for example, it can be used to capture events for unit-testing or to send it through some more complex setup that requires proxy authentication.

```
<code snippet for this platform/language>
```

`httpProxy`

When set, a proxy can be configured that should be used for outbound requests. This is also used for HTTPS requests unless a separate `https-proxy` is configured. However, not all SDKs support a separate HTTPS proxy. SDKs will attempt to default to the system-wide configured proxy if possible. For instance, on Unix systems, the `http_proxy` environment variable will be picked up.

`httpsProxy`

Configures a separate proxy for outgoing HTTPS requests. This value might not be supported by all SDKs. When not supported the `http-proxy` value is also used for HTTPS requests at all times.

`shutdownTimeout`

Controls how many seconds to wait before shutting down. Sentry SDKs send events from a background queue and this queue is given a certain amount to drain pending events. The default is SDK specific but typically around 2 seconds. Setting this value too low will most likely cause problems for sending events from command line applications. Setting it too high will cause the application to block for a long time for users with network connectivity problems.