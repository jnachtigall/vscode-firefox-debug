# VS Code Debug Adapter for Firefox

A Visual Studio Code extension to debug your web application in Firefox.

## Features
* Line breakpoints
* Conditional breakpoints
* Exception breakpoints (caught and uncaught)
* Breaking on `debugger` statements
* Step over, step in, step out
* Stepping into scripts passed to eval()
* Inspecting stackframes, object properties (including prototypes) and return values
* Watches
* Evaluating javascript expressions in the debug console of VS Code
* Sourcemaps - these are handled by Firefox, so if they work in the built-in Firefox debugger,
  they should also work in VS Code
* Debugging WebWorkers
* Debugging multiple browser tabs

## Starting
You can use this extension in launch or attach mode. 
In launch mode it will start an instance of Firefox navigated to the start page of your application
and terminate it when you stop debugging.
In attach mode it attaches to a running instance of Firefox.

To configure these modes you must create a file `.vscode/launch.json` in the root directory of your
project. You can do so manually or let VS Code create an example configuration for you by clicking 
the gear icon at the top of the Debug pane.

### Launch
Here's an example configuration for launching Firefox navigated to the local file `index.html` 
in the root directory of your project:
```
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch index.html",
            "type": "firefox",
            "request": "launch",
            "file": "${workspaceRoot}/index.html"
        }
    ]
}
```

You may want (or need) to debug your application running on a Webserver (especially if it interacts
with server-side components like Webservices). In this case replace the `file` property in your
launch configuration with a `url` and a `webRoot` property. These properties are used to map
urls to local files:
```
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch localhost",
            "type": "firefox",
            "request": "launch",
            "url": "http://localhost/index.html",
			"webRoot": "${workspaceRoot}"
        }
    ]
}
```

### Attach
To use attach mode, you have to launch Firefox manually from a terminal with remote debugging enabled.
Note that you must first configure Firefox to allow remote debugging. To do this, open the Firefox 
configuration page by entering `about:config` in the address bar. Then set the following preferences:

Preference Name                       | Value   | Comment
--------------------------------------|---------|---------
`devtools.debugger.remote-enabled`    | `true`  | Required
`devtools.chrome.enabled`             | `true`  | Required
`devtools.debugger.workers`           | `true`  | Required if you want to debug WebWorkers
`devtools.debugger.prompt-connection` | `false` | Recommended
`devtools.debugger.force-local`       | `false` | Set this only if you want to attach VS Code to Firefox running on a different machine (using the `host` property in the `attach` configuration)

Then close Firefox and start it from a terminal like this:

__Windows__

`"C:\Program Files\Mozilla Firefox\firefox.exe" -start-debugger-server -no-remote`

__OS X__

`/Applications/Firefox.app/Contents/MacOS/firefox -start-debugger-server -no-remote`

__Linux__

`firefox -start-debugger-server -no-remote`

Navigate to your web application and use this `launch.json` configuration to attach to Firefox:
```
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch index.html",
            "type": "firefox",
            "request": "attach"
        }
    ]
}
```

If your application is running on a Webserver, you need to add the `url` and `webRoot` properties
to the configuration (as in the second launch configuration example above).

### Optional configuration properties
* `profileDir`, `profile`: You can specify a Firefox profile directory or the name of a profile
  created with the Firefox profile manager. Otherwise, a profile directory will be created
  in the system's temporary folder and it will be automatically configured to allow remote
  debugging. If you specify a profile directory which doesn't exist yet, it will also be created
  and configured automatically.
* `port`: Firefox uses port 6000 for the debugger protocol by default. If you want to use a different
  port, you can set it with this property.
* `firefoxExecutable`: The absolute path to the Firefox executable (`launch` configuration only). For instance, for the Firefox Developer Edition you might want to set this to something like `"firefoxExecutable": "C:/Program Files/Firefox Developer Edition/firefox.exe"`. If not specified, this extension will use the Firefox' default installation path.
* `firefoxArgs`: An array of additional arguments used when launching Firefox (`launch` configuration only)
* `host`: If you want to debug with Firefox running on different machine, you can specify the 
  device's address using this property (`attach` configuration only).

## Troubleshooting
* Sometimes when using a `launch` configuration you may get a message saying that Firefox was
  closed unexpectedly. If this happens, click "Start in Safe Mode" and then close Firefox manually
  and stop the VS Code debugger by clicking the stop button twice.
  Afterwards, you should be able to launch it again.
  This is due to [Firefox bug #336193](https://bugzilla.mozilla.org/show_bug.cgi?id=336193).
* If you think you've found a bug in this adapter please [file a bug report](https://github.com/hbenl/vscode-firefox-debug/issues).

## Changelog

### Version 0.6.2
* bugfix: stepping and resuming stopped working if a breakpoint was hit immediately after loading the page

### Version 0.6.1
* Fix debugging WebWorkers and multiple browser tabs in VSCode 1.2.0

### Version 0.6.0
* Add support for evaluating javascript expressions in the debug console even if Firefox isn't paused
* Add support for debugger statements

### Version 0.5.0
* Add support for call stack paging

### Version 0.4.0
* Add support for debugging WebWorkers
* Add support for debugging multiple browser tabs
* Fix exception breakpoints in VSCode 1.1.0
* Re-create the Firefox profile on every launch, unless a profile name or directory is configured

### Version 0.3.0
* Print messages from the Firefox console in the VS Code debug console
* bugfix: resume the VS Code debugger when Firefox resumes, e.g. if the user reloads the page in 
  Firefox while the debugger is paused

### Version 0.2.0
* Automatically create a Firefox profile for debugging

### Version 0.1.0
* Initial release
 
