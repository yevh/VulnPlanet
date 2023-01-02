# A03 2021 – Injection

An application is vulnerable to attack when:

1. User-supplied data is not validated, filtered, or sanitized by the application.
2. Dynamic queries or non-parameterized calls without context-aware escaping are used directly in the interpreter.
3. Hostile data is used within object-relational mapping (ORM) search parameters to extract additional, sensitive records.
4. Hostile data is directly used or concatenated. The SQL or command contains the structure and malicious data in dynamic queries, commands, or stored procedures. 

- **Coverage** 
     - JS
     - Java
     - Python
     - PHP

# JS

## Vulnureable code example

Node.js provides several ways to execute external programs as part of the child_process package. The exec method spawns a shell then executes the command within that shell. the execSync (synchronous version of exec) to run a system command with unsanitized user input.

```
const output = child_process.execSync(`ping -c 1 '${destination}'`);
```

## Prevention code example

. If this is not possible, execution of external commands should be handled by using other methods (execFile and spawn) to directly invoke the desired command, instead of by invoking a system shell.

```
const output = child_process.spawnSync('ping', ['-c', '1', destination]);
```


# Java

## Vulnureable code example

Java method invokes Runtime.exec(), which receives unsanitized data originating from the environment, making this code susceptible to a command injection attack.

```
public static void listFiles(String dir) throws Exception {
  Runtime rt = Runtime.getRuntime();
  Process proc = rt.exec(new String[] {"sh", "-c", "ls " + dir});
  int result = proc.waitFor();
  if (result != 0) {
    System.out.println("process error: " + result);
  }
  InputStream in = (result == 0) ? proc.getInputStream() :
                                   proc.getErrorStream();
  int c;
  while ((c = in.read()) != -1) {
    System.out.print((char) c);
  }
}
```

## Prevention code example

Sanitize the untrusted user input by permitting only a small group of allowed characters in the argument that will be passed to Runtime.exec(); all other characters are excluded.

```
// ...
if (!Pattern.matches("[0-9A-Za-z@.]+", dir)) {
  // Handle error
}
// ...
```


# Python

## Vulnureable code example

Flask web application executes the nslookup command to resolve the host supplied by the user.

```
@app.route("/dns")
def page():

    hostname = request.values.get(hostname)
    cmd = 'nslookup ' + hostname

    return subprocess.check_output(cmd, shell=True)
```

## Prevention code example

Execute commands using the subprocess API, passing the command as a list of argument strings with the shell option set to False. Passing the command as a list of arguments is the safer approach that should always be used, but it might be vulnerable to argument injection depending on the binary.

```
subprocess.Popen([ 'nslookup', hostname ], ... , shell=False)
```

# PHP

## Vulnureable code example

```escapeshellcmd``` doesn’t prevent additional arguments from being injected. The attacker can cat any files, not only the ones suffixed with "_public.txt",
   by passing additional arguments such as "data_private.csv /tmp/data"

```
exec(escapeshellcmd("/usr/bin/cat /tmp/".$_GET["arg"]."_public.txt"));
```

## Prevention code example

```escapeshellarg``` should be used to sanitize a specific argument:

```
exec("/usr/bin/cat ".escapeshellarg("/tmp/".$_GET["arg"]."_public.txt"));
```
