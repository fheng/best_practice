# Bash style guide

This style guide would outlines how to write bash scripts.
This guide is based on:

http://mywiki.wooledge.org/BashGuide/Practices

and Google style guide:

https://google.github.io/styleguide/shell.xml

If anything is not mentioned explicitly in this guide, it defaults to Google style guide.


### Functions

Don't use the `function` keyword.  All variables created in a function should be
made local.

``` bash
# wrong
function foo {
    i=foo # this is now global, wrong
}

# right
foo() {
    local i=foo # this is local, preferred
}
```

### Block Statements

`then` should be on the same line as `if`, and `do` should be on the same line
as `while`.

``` bash
# wrong
if true
then
    ...
fi

# also wrong, though admittedly looks kinda cool
true && {
    ...
}

# right
if true; then
    ...
fi
```

### Spacing

No more than 2 consecutive newline characters (ie. no more than 1 blank line in a row)

### Comments

No explicit style guide for comments.  Don't change someone's comments for aesthetic reasons
unless you are rewriting or updating them.


### `test(1)`

Use `[[ ... ]]` for conditional testing, not `[ .. ]` or `test ...`

``` bash
# wrong
test -d /etc

# also wrong
[ -d /etc ]

# correct
[[ -d /etc ]]
```

### Command Substitution

Use `$(...)` for command substitution.

``` bash
foo=`date`  # wrong
foo=$(date) # right
```

### Parameter Expansion

Always prefer [parameter expansion]
over external commands like `echo`, `sed`, `awk`, etc.

``` bash
name='bahamas10'

# wrong
prog=$(basename "$0")
nonumbers=$(echo "$name" | sed -e 's/[0-9]//g')

# right
prog=${0##*/}
nonumbers=${name//[0-9]/}
```

### Listing Files

Do not [parse ls(1)](http://mywiki.wooledge.org/ParsingLs), instead use
bash builtin functions to loop files

``` bash
# very wrong, potentially unsafe
for f in $(ls); do
    ...
done

# right
for f in *; do
    ...
done
```

### Determining path of the executable

Simply stated, you can't know this for sure.  If you are trying to find
out the full path of the executing program, you should rethink your software
design.

### Arrays and lists

Use bash arrays instead of a string separated by spaces (or newlines, tabs, etc.)
whenever possible

``` bash
# wrong
modules='json httpserver jshint'
for module in $modules; do
    npm install -g "$module"
done

# right
modules=(json httpserver jshint)
for module in "${modules[@]}"; do
    npm install -g "$module"
done
```

### Cat usage

Don't use `cat(1)` when you don't need it.  

``` bash
# wrong
cat file | grep foo

# right
grep foo < file

# also right
grep foo file
```

Prefer using a command line tool's builtin method of reading a file instead of
passing in stdin.  This is where we make the inference that, if a program says
it can read a file passed by name, it's probably more performant to do so.

Style
-----

### Quoting

Use double quotes for strings that require variable expansion or command
substitution interpolation, and single quotes for all others.

``` bash
# right
foo='Hello World'
bar="You are $USER"

# wrong
foo="hello world"

# possibly wrong, depending on intent
bar='You are $USER'
```

All variables that will undergo word-splitting *must* be quoted (1).  If no splitting
will happen, the variable may remain unquoted.

``` bash
foo='hello world'

if [[ -n $foo ]]; then   # no quotes needed - [[ ... ]] won't word-split variable expansions
    echo "$foo"          # quotes needed
fi

bar=$foo  # no quotes needed - variable assignment doesn't word-split
```

1. The only exception to this rule is if the code or bash controls the variable for the
duration of its lifetime.  For instance, [basher](https://github.com/bahamas10/basher) has code like:

``` bash
printf_date_supported=false
if printf '%()T' &>/dev/null; then
    printf_date_supported=true
fi

if $printf_date_supported; then
    ...
fi
```

Even though `$printf_date_supported` undergoes word-splitting in the `if`
statement in that example, quotes are not used because the contents of that
variable are controlled explicitly and not taken from a user or command.

Also, variables like `$$`, `$?`, `$#`, etc. don't require quotes because they
will never contain spaces, tabs, or newlines.

When in doubt however, [quote all expansions](http://mywiki.wooledge.org/Quotes).

### Variable Declaration

Avoid uppercase variable names unless there's a good reason to use them.
Don't use `let` or `readonly` to create variables.  `declare` should *only*
be used for associative arrays.  `local` should *always* be used in functions.

``` bash
# wrong
declare -i foo=5
let foo++
readonly bar='something'
FOOBAR=baz

# right
i=5
((i++))
bar='something'
foobar=baz
```

### shebang

Bash is not always located at `/bin/bash`, so always use this line:

``` bash
#!/usr/bin/env bash
```

### Error Checking

`cd`, for example, doesn't always work.  Make sure to check for any possible errors
for `cd` (or commands like it) and exit or break if they are present.

``` bash
# wrong
cd /some/path # this could fail
rm file       # if cd fails where am I? what am I deleting?

# right
cd /some/path || exit
rm file
```

### `set -e`

Always check if your script should exit on error - in most cases it should help prevent bugs.
Like in C, sometimes you want an error, or you expect something to fail, and that doesn't necessarily mean you want the program
to exit.
