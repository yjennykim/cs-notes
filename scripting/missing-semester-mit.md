Notes are from Shell Tools and Scripting chapter of `./missing-semester` course

### Scripting
- Use "" delimiter to substitute variable values and '' delimiter for literal strings.
```bash
    // $1 is first argument to script 
    // -p=path designation, default is to create a subdirectory within the current directory.
    mcd () {
        mkdir -p "$1"
        cd "$1"
    }
```
Special variables
`$_` : last argument of the previous command
`$0` : script name
`$1` to `$9` : arguments to the script
`$@` : all the arguments
`$#` : number of arguments
`$$` : pid 
`$?` : return code of the previous command
`!! [tab]`: entire last command, include arguments

Example: 
```bash
Documents % mkdir cs-notes
Documents % cd $_
cs-notes % git init
```

### Command substitution
`$()` : command substitution, which captures the output of the command inside the parentheses.

Example:
```bash
cs-notes % path=$(pwd)
cs-notes % echo $path
```

```bash
for file in $(ls)
```
Examples of expanding:
`touch foo foo1 foo2 foo10`
`touch project{1,2}/src/test/test{1,2,3}.py` 
`touch {foo,bar}/{a...j}` - expand foo/a, ..., foo/j then bar/a, ..., bar/j

`shellcheck <script.sh>` to perform static analysis on bugs in shell script

The kernel can be told to use python interpreter instead of a shell command by including a <b>shebang</b> line at the top of the script. 
```bash
#!/usr/local/bin/python
```
`env` will make use of the `PATH` environment to resolve the location

### Searching for Files
`-type` : (d), file (f)
`-mtime`: (m)odified in last day
`find . -name src -type d -mtime -1`

execute a command after finding the files
`find . -name src -type d` - find all directories named src
`find . -name "*.tmp"`
`find . -name "*.tmp" -exec rm {} \;"`
`find . -path '*/test/*.py' -type f` - find all python files that have a folder named test in their path
`find . -mtime -1` - find all files modified in the last day

`echo $?` - prints the exit status of the last command executed.

### Perform actions on found files
The command:
```
find . -name '*.png' -exec convert {} {}.jpg `;
find . -name '*.tmp' -exec rm {} \;
```

`find .` → Starts the search in the current directory (.) and all its subdirectories.
`-name '*.tmp'` → Looks for files with the .tmp extension.
`-exec` → Executes the specified command (rm) on each matching file.
`rm {}` → The {} is a placeholder for the current file being processed.
`\;` → Terminates the -exec command.

The `\;` ensures that the rm command is run once per file.


### Searching with Grep and Ripgrep
`grep` : search for substring
`grep -R foobar .` : search recursively for foobar 
`rg "import requests" -t py ~/scratch -C 5` : (ripgrep) similar to grep but with color coding and extra info. `-C` to search with context.
`rg -u --files-without-match "^#\!" -t sh` : It finds all .sh files that do NOT have a shebang line (#!) at the beginning.

### History
`history | grep curl` - search for commands with `curl` command

### Tools
`broot` : for searching directories
`fzf` : fuzzy finder to match through history
`tldr` : simplified man pages with examples