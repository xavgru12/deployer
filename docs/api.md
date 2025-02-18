<!-- DO NOT EDIT THIS FILE! -->
<!-- Instead edit src/functions.php -->
<!-- Then run bin/docgen -->

# API Reference

## host()

```php
host(string ...$hostname)
```

Defines a host or hosts.
```php
host('example.org');
host('prod.example.org', 'staging.example.org');
```

Inside task can be used to get `Host` instance of an alias.
```php
task('test', function () {
    $port = host('example.org')->get('port');
});
```



## localhost()

```php
localhost(string ...$hostnames)
```



## currentHost()

```php
currentHost(): Host
```

Returns current host.


## select()

```php
select(string $selector): array
```

Returns hosts based on provided selector.

```php
on(select('stage=prod, role=db'), function (Host $host) {
    ...
});
```



## selectedHosts()

```php
selectedHosts(): array
```

Returns array of hosts selected by user via CLI.



## import()

```php
import(string $file): void
```

Import other php or yaml recipes.

```php
import('recipe/common.php');
```

```php
import(__DIR__ . '/config/hosts.yaml');
```



## desc()

```php
desc(?string $title = null): ?string
```

Set task description.


## task()

```php
task(string $name, $body = null): Task
```

Define a new task and save to tasks list.

Alternatively get a defined task.



| Argument | Type | Comment |
|---|---|---|
| `$name` | `string` | Name of current task. |
| `$body` | `callable():void` or `array` or `null` | Callable task, array of other tasks names or nothing to get a defined tasks |

## before()

```php
before(string $task, $do)
```

Call that task before specified task runs.




| Argument | Type | Comment |
|---|---|---|
| `$task` | `string` | The task before $that should be run. |
| `$do` | `string` or `callable():void` | The task to be run. |

## after()

```php
after(string $task, $do)
```

Call that task after specified task runs.




| Argument | Type | Comment |
|---|---|---|
| `$task` | `string` | The task after $that should be run. |
| `$do` | `string` or `callable():void` | The task to be run. |

## fail()

```php
fail(string $task, $do)
```

Setup which task run on failure of $task.
When called multiple times for a task, previous fail() definitions will be overridden.




| Argument | Type | Comment |
|---|---|---|
| `$task` | `string` | The task which need to fail so $that should be run. |
| `$do` | `string` or `callable():void` | The task to be run. |

## option()

```php
option(string $name, $shortcut = null, ?int $mode = null, string $description = '', $default = null): void
```

Add users options.



| Argument | Type | Comment |
|---|---|---|
| `$name` | `string` | The option name |
| `$shortcut` | `string` or `array` or `null` | The shortcuts, can be null, a string of shortcuts delimited by | or an array of shortcuts |
| `$mode` | `int` or `null` | The option mode: One of the VALUE_* constants |
| `$description` | `string` | A description text |
| `$default` | `string` or `string[]` or `int` or `bool` or `null` | The default value (must be null for self::VALUE_NONE) |

## cd()

```php
cd(string $path): void
```

Change the current working directory.


## within()

```php
within(string $path, callable $callback)
```

Execute a callback within a specific directory and revert back to the initial working directory.



## run()

```php
run(string $command, ?array $options = [], ?int $timeout = null, ?int $idle_timeout = null, ?string $secret = null, ?array $env = null, ?bool $real_time_output = false, ?bool $no_throw = false): string
```

Executes given command on remote host.

Examples:

```php
run('echo hello world');
run('cd {{deploy_path}} && git status');
run('password %secret%', secret: getenv('CI_SECRET'));
run('curl medv.io', timeout: 5);
```

```php
$path = run('readlink {{deploy_path}}/current');
run("echo $path");
```




| Argument | Type | Comment |
|---|---|---|
| `$command` | `string` | Command to run on remote host. |
| `$options` | `array` or `null` | Array of options will override passed named arguments. |
| `$timeout` | `int` or `null` | Sets the process timeout (max. runtime). The timeout in seconds (default: 300 sec; see {{default_timeout}}, `null` to disable). |
| `$idle_timeout` | `int` or `null` | Sets the process idle timeout (max. time since last output) in seconds. |
| `$secret` | `string` or `null` | Placeholder `%secret%` can be used in command. Placeholder will be replaced with this value and will not appear in any logs. |
| `$env` | `array` or `null` | Array of environment variables: `run('echo $KEY', env: ['key' => 'value']);` |
| `$real_time_output` | `bool` or `null` | Print command output in real-time. |
| `$no_throw` | `bool` or `null` | Don't throw an exception of non-zero exit code. |

## runLocally()

```php
runLocally(string $command, ?array $options = [], ?int $timeout = null, ?int $idle_timeout = null, ?string $secret = null, ?array $env = null, ?string $shell = null): string
```

Execute commands on a local machine.

Examples:

```php
$user = runLocally('git config user.name');
runLocally("echo $user");
```




| Argument | Type | Comment |
|---|---|---|
| `$command` | `string` | Command to run on localhost. |
| `$options` | `array` or `null` | Array of options will override passed named arguments. |
| `$timeout` | `int` or `null` | Sets the process timeout (max. runtime). The timeout in seconds (default: 300 sec, `null` to disable). |
| `$idle_timeout` | `int` or `null` | Sets the process idle timeout (max. time since last output) in seconds. |
| `$secret` | `string` or `null` | Placeholder `%secret%` can be used in command. Placeholder will be replaced with this value and will not appear in any logs. |
| `$env` | `array` or `null` | Array of environment variables: `runLocally('echo $KEY', env: ['key' => 'value']);` |
| `$shell` | `string` or `null` | Shell to run in. Default is `bash -s`. |

## test()

```php
test(string $command): bool
```

Run test command.
Example:

```php
if (test('[ -d {{release_path}} ]')) {
...
}
```



## testLocally()

```php
testLocally(string $command): bool
```

Run test command locally.
Example:

    testLocally('[ -d {{local_release_path}} ]')



## on()

```php
on($hosts, callable $callback): void
```

Iterate other hosts, allowing to call run a func in callback.

```php
on(select('stage=prod, role=db'), function ($host) {
    ...
});
```

```php
on(host('example.org'), function ($host) {
    ...
});
```

```php
on(Deployer::get()->hosts, function ($host) {
    ...
});
```



## invoke()

```php
invoke(string $taskName): void
```

Runs a task.
```php
invoke('deploy:symlink');
```



## upload()

```php
upload($source, string $destination, array $config = []): void
```

Upload files or directories to host.

> To upload the _contents_ of a directory, include a trailing slash (eg `upload('build/', '{{release_path}}/public');`).  
> Without the trailing slash, the build directory itself will be uploaded (resulting in `{{release_path}}/public/build`).  

 The `$config` array supports the following keys:

- `flags` for overriding the default `-azP` passed to the `rsync` command
- `options` with additional flags passed directly to the `rsync` command
- `timeout` for `Process::fromShellCommandline()` (`null` by default)
- `progress_bar` to display upload/download progress
- `display_stats` to display rsync set of statistics

Note: due to the way php escapes command line arguments, list-notation for the rsync `--exclude={'file','anotherfile'}` option will not work.  
A workaround is to add a separate `--exclude=file` argument for each exclude to `options` (also, _do not_ wrap the filename/filter in quotes).
An alternative might be to write the excludes to a temporary file (one per line) and use `--exclude-from=temporary_file` argument instead.




## download()

```php
download(string $source, string $destination, array $config = []): void
```

Download file or directory from host




## info()

```php
info(string $message): void
```

Writes an info message.


## warning()

```php
warning(string $message): void
```

Writes an warning message.


## writeln()

```php
writeln(string $message, int $options = 0): void
```

Writes a message to the output and adds a newline at the end.


## parse()

```php
parse(string $value): string
```

Parse set values.


## set()

```php
set(string $name, $value): void
```

Setup configuration option.


## add()

```php
add(string $name, array $array): void
```

Merge new config params to existing config array.



## get()

```php
get(string $name, $default = null)
```

Get configuration value.




## has()

```php
has(string $name): bool
```

Check if there is such configuration option.


## ask()

```php
ask(string $message, ?string $default = null, ?array $autocomplete = null): ?string
```



## askChoice()

```php
askChoice(string $message, array $availableChoices, $default = null, bool $multiselect = false)
```



## askConfirmation()

```php
askConfirmation(string $message, bool $default = false): bool
```



## askHiddenResponse()

```php
askHiddenResponse(string $message): string
```



## input()

```php
input(): InputInterface
```



## output()

```php
output(): OutputInterface
```



## commandExist()

```php
commandExist(string $command): bool
```

Check if command exists



## commandSupportsOption()

```php
commandSupportsOption(string $command, string $option): bool
```



## which()

```php
which(string $name): string
```



## remoteEnv()

```php
remoteEnv(): array
```

Returns remote environments variables as an array.
```php
$remotePath = remoteEnv()['PATH'];
run('echo $PATH', env: ['PATH' => "/home/user/bin:$remotePath"]);
```


## error()

```php
error(string $message): Exception
```

Creates a new exception.


## timestamp()

```php
timestamp(): string
```

Returns current timestamp in UTC timezone in ISO8601 format.


## fetch()

```php
fetch(string $url, string $method = 'get', array $headers = [], ?string $body = null, ?array &$info = null, bool $nothrow = false): string
```

Example usage:
```php
$result = fetch('{{domain}}', info: $info);
var_dump($info['http_code'], $result);
```


