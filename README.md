# Bash Service Manager
Bash Script's for Service Manager

Create your custom service for development.

## Usage ##

1. Create your _service script_.
2. Define the configuration variables: `PID_FILE_PATH`, `LOG_FILE_PATH` and `LOG_ERROR_FILE_PATH`.
2. Copy `services.sh` content or import it.
3. Assign $action, $serviceName, $command (mandatory); $workDir, $onStart, $onFinish (optional) variables 
4. Call `serviceMenu` function
5. Make your new _service script_ executable: `chmod a+x my-service-script`
6. Use it!

## Configuration Variables ##

### PID_FILE_PATH ###

Configure the `PID_FILE_PATH` variable before import `service.sh` script, and define the **PID** file path.

### LOG_FILE_PATH ###

Configure the `LOG_FILE_PATH` variable before import `service.sh` script, and define the **LOG** file path.

### LOG_ERROR_FILE_PATH ###

Configure the `LOG_ERROR_FILE_PATH` variable before import `service.sh` script, and define the **ERROR** file path.

## servicesMenu function ##

Just call only this function to make everything work!

### Variables ###

#### 1: action ####

**mandatory**

This is the action to execute. Please see _Actions_ section below for more information.

If it is an invalid action or emtpy action, you can see the _help_.

#### 2: serviceName ####

**mandatory**

This is the user friendly _Service Name_.

#### 3: command ####

**mandatory, array variable**

This is the _commands_ that you must execute to start _your service_.

**Parameters:**

1. `action`: Caller script action (`start`, `stop`, `restart`, `status`, `run`, `debug`, `tail`, `tail-log` or `tail-error`).

#### 4: workDir ####

**optional**

The working directory is set, where it must be located to execute the _COMMAND_.

#### 5: onStart ####

**optional**

Commands to execute before _Service_ start.

If function exit code is not `0` (zero), the service will not started.

**Parameters:**

1. `action`: Caller script action (`start`, `stop`, `restart`, `status`, `run`, `debug`, `tail`, `tail-log` or `tail-error`).

#### 6: onFinish ####

**optional**

Commands to execute after _Service_ finish/exit.

**Parameters:**

1. `action`: Caller script action (`start`, `stop`, `restart`, `status`, `run`, `debug`, `tail`, `tail-log` or `tail-error`).
2. `serviceExitCode`: The **COMMAND** _Exit Code_.

## Actions ##

If it is an invalid action or emtpy action, you can see the _help_.

* `start`: Start the _service_.
* `stop`: Stop the _service_.
* `restart`: Restart the _service_. If the _service_ is running, first call _stop_ then call _start_.
* `status`: Get _service_ status.
* `tail`: See all _service_ output.
* `tail-log`: See _service_ _std_ output.
* `tail-error`: See _service_ _err_ output.
* `run`: Execute _service command_ and exit (this action **does not** stop the _service_).
* `debug`: Stop _service_ (if running) and run _service command_ and exit.

## Examples ##

### Telegraf Service ###

**telegraf.sh** file:

```bash
#!/usr/bin/env bash

appDir=$(cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null && pwd)

export LOG_FILE_PATH="$serviceName.log"
export LOG_ERROR_FILE_PATH="$serviceName.error.log"
export PID_FILE_PATH="$serviceName.pid"

. "$appDir/services.sh"

# Action to execute (mandatoty)
action="$1"  
# Friendly service name (mandatoty)
serviceName= "telegraf"
# Command to run (mandatoty, array variable)
command=(./telegraf --config "telegraf.conf")
# Working Directory (optional)
workDir="$appDir"
# On start (optional)
#onStart=
# On finish (optional)
#onFinish=

serviceMenu
```

In console:

```bash
$ telegraf.sh status
$ telegraf.sh restart
```

### Custom Service ###

**my-service** file:

```bash
#!/usr/bin/env bash

export PID_FILE_PATH="my-service.pid"
export LOG_FILE_PATH="my-service.log"
export LOG_ERROR_FILE_PATH="my-service.error.log"

. ./services.sh

# Action to execute (mandatoty)
action="$1"  
# Friendly service name (mandatoty)
serviceName="Example Service"
# Command to run (mandatoty, array variable)
command=("ping 1.1.1.1")
# Working Directory (optional)
#workDir=
# On start (optional)
#onStart=
# On finish (optional)
#onFinish=

serviceMenu
```
