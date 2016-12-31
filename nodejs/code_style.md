# JavaScript Code Style Guide
This guide outlines some basic code-style requirements which we have in RHMAP. These mostly to give our components consistency, particularly as we move towards open sourcing the rest of our platform.
We are currently using JSHint for JavaScript linting, but are leaning towards replacing it with the more modern (and well-maintained) ESLint.

### ESLint

Below is our standard ESLint configs for our Node.js & Browser components

```
{
  "env": {
    "es6": true,
    "node": true,
    "mocha": true
  },
  "extends": "eslint:recommended",
  "rules": {
    "array-callback-return": "warn",
    "brace-style": ["error", "1tbs"],
    "complexity": ["warn", 20],
    "eqeqeq": "error",
    "guard-for-in": "error",
    "indent": ["error", 2],
    "linebreak-style": ["error", "unix"],
    "no-array-constructor": "error",
    "no-console": "warn",
    "no-lonely-if": "warn",
    "no-loop-func": "warn",
    "no-mixed-spaces-and-tabs": ["error"],
    "no-nested-ternary": "error",
    "no-spaced-func": "error",
    "no-trailing-spaces": "error",
    "semi": ["error", "always"],
    "space-before-blocks": "error",
    "space-before-function-paren": ["error", "never"],
    "keyword-spacing": ["error"],
    "curly": ["error", "all"],
    "no-unused-vars": ["error", { "argsIgnorePattern": "next" }],
    "no-const-assign": "error",
    "no-use-before-define": ["error", "nofunc"],
    "block-scoped-var": "error",
    "prefer-const": "error",
    "prefer-template": "warn",
    "template-curly-spacing": ["error", "never"],
    "no-confusing-arrow": ["error", {"allowParens": true}],
    "arrow-parens": ["error", "as-needed"],
    "arrow-body-style": ["error", "as-needed"],
    "generator-star-spacing": ["error", "after"],
    "require-yield": "error"
  }
}

```

There are two small differences for Browser components, the rest is the same:

* env.browser: true - to add an expectation for things like the window global
* globals: {} - to add an expectation on some other globals currently coming from script includes in a Browser

This rule-set extends from "eslint:recommended" - to see what's covered in this ruleset, see: http://eslint.org/docs/rules/

#### [array-callback-return](http://eslint.org/docs/rules/array-callback-return)
Array has several methods for filtering, mapping, and folding. If we forget to write return statement in a callback of those, it’s probably a mistake.

#### [brace-style](http://eslint.org/docs/rules/brace-style)
Our brace style is 1TBS - see https://en.wikipedia.org/wiki/Indent_style#Variant:_1TBS

#### [complexity](http://eslint.org/docs/rules/complexity)
Cyclomatic complexity measures the number of linearly independent paths through a program’s source code.
If code-paths are flagged via ESLint with a complexity warning, there's likely code-smell that should be addressed.

#### [eqeqeq](http://eslint.org/docs/rules/eqeqeq)
To avoid type coercion, we recommend using `===` and `!==` instead of `==` and `!=`.

#### [guard-for-in](http://eslint.org/docs/rules/guard-for-in)
Looping over objects with a for in loop will include properties that are inherited through the prototype chain - this usually has unintended consequences.

#### [indent](http://eslint.org/docs/rules/indent)
2 spaces for indentation

#### [linebreak-style](http://eslint.org/docs/rules/linebreak-style)
Unix linebreaks

#### [no-array-constructor](http://eslint.org/docs/rules/no-array-constructor)
Don't use:

    var array = new Array();

Use:

    var array = [];

#### [no-console](http://eslint.org/docs/rules/no-console)
Don't use `console.<log|error|warn>`, use a Winston/Bunyan logger and level instead

#### [no-lonely-if](http://eslint.org/docs/rules/no-lonely-if)
Prefer `else if` over a lonely `if` inside of an `else` block.

#### [no-loop-func](http://eslint.org/docs/rules/no-loop-func)
Writing functions within loops tends to result in errors due to the way the function creates a closure around the loop, so we tend to avoid it.

#### [no-mixed-spaces-and-tabs](http://eslint.org/docs/rules/no-mixed-spaces-and-tabs)
Don't mix tabs and spaces

#### [no-nested-ternary](http://eslint.org/docs/rules/no-nested-ternary)
Ternary are operators are okay, but don't go nuts and nest them. Hell is:

    var foo = bar ? baz : qux === quxx ? bing : bam;

#### [no-spaced-func](http://eslint.org/docs/rules/no-spaced-func)
Almost all of our code does not use a space between the `function` keyword and following parenthesis.

Don't use:

    fn ()

    fn
    ()

Use:

    fn()

#### [no-trailing-spaces](http://eslint.org/docs/rules/no-trailing-spaces)
Don't leave trailing spaces on lines - they make diffs difficult to read.

#### [semi](http://eslint.org/docs/rules/semi)
Always use semi-colons.

#### [space-before-blocks](http://eslint.org/docs/rules/space-before-blocks)
For consistency, we prefer:

    if (a) {
      b();
    }

Instead of:

    if (a){
      b();
    }

#### [space-before-function-paren](http://eslint.org/docs/rules/space-before-function-paren)
For consistency, we prefer:

    function foo() {
      // ...
    }

Over:

    function foo () {
      // ...
    }

#### [keyword-spacing](http://eslint.org/docs/rules/keyword-spacing)
For consistency, we prefer:

    if (condition) {
      // ...
    }

Over:

    if(condition) {
      // ...
    }

#### [curly](http://eslint.org/docs/rules/curly)
Always use curly braces, even if only one statement per block.
SonarQube will fail on this also.

E.g., prefer:

    if (err) {
      return err;
    }

Over:

    if (err) return err;

#### [no-unused-vars](http://eslint.org/docs/rules/no-unused-vars)
No unused variables: if there are no uses of a variable, then it
should be removed.

The exception to this, is if the variable is called `next`; this
allows us to specify the rght number of parameters to ExpressJS, when
they're implicitly needed by the framework (different behaviour based
on the number of parameters to some functions), but not explicitly
called by us.

#### [no-const-assign](http://eslint.org/docs/rules/no-const-assign)
We cannot modify variables that are declared using const keyword. It
will raise a runtime error. If something needs to be re-assigned to a
different value, use `let` instead.

#### [no-use-before-define](http://eslint.org/docs/rules/no-use-before-define)
In JavaScript, prior to ES6, variable and function declarations are
hoisted to the top of a scope, so it’s possible to use identifiers
before their formal declarations in code. This can be confusing and
some believe it is best to always declare variables and functions
before using them.

In ES6, block-level bindings (`let` and `const`) introduce a “temporal
dead zone” where a ReferenceError will be thrown with any attempt to
access the variable before its declaration.

We're using the `"nofunc"` option here also, which will allow us to
use functions that are declared later in the file, as they're always
hoisted.

#### [block-scoped-var](http://eslint.org/docs/rules/block-scoped-var)
This rule generates warnings when variables are used outside of the
block in which they were defined. This is to get `var` to emulate
`let` until the latter is performant enough to start using.

#### [prefer-const](http://eslint.org/docs/rules/prefer-const)
If a variable is never reassigned, using the `const` declaration is
better. It tells readers, “this variable is never reassigned,”
reducing cognitive load and improving maintainability.

#### [prefer-template](http://eslint.org/docs/rules/prefer-template)
In ES2015 (ES6), we can use template literals instead of string
concatenation. This rule is aimed to flag usage of `+` operators with
strings.

E.g., prefer:

    const str = `Hello, ${name}!`;

over:

    const str = "Hello, " + name + "!"`;

#### [template-curly-spacing](http://eslint.org/docs/rules/template-curly-spacing)
We can embed expressions in template strings with using a pair of `${`
and `}`. This rule aims to maintain consistency around the spacing
inside of template literals.

E.g.:

    const hello = `hello, ${people.name}!`;

over:

    const hello = `hello, ${ people.name}!`;

#### [no-confusing-arrow](http://eslint.org/docs/rules/no-confusing-arrow)
Arrow functions (`=>`) are similar in syntax to some comparison
operators (`>`, `<`, `<=`, and `>=`). This rule warns against using
the arrow function syntax in places where it could be confused with a
comparison operator. We use the `allowParens` option, which allows the
use of parens to de-confuse.

#### [arrow-parens](http://eslint.org/docs/rules/arrow-parens)
Arrow functions can omit parentheses when they have exactly one
parameter. In all other cases the parameter(s) must be wrapped in
parentheses. This rule enforces the consistent use of parentheses in
arrow functions.

E.g.:

    const a = [1, 2, 3].map(x => 2 * x);

over:

    const a = [1, 2, 3].map((x) => 2 * x);

#### [arrow-body-style](http://eslint.org/docs/rules/arrow-body-style)
Arrow functions have two syntactic forms for their function
bodies. They may be defined with a block body (denoted by curly
braces) `() => { ... }` or with a single expression `() => ...`, whose
value is implicitly returned. This rule enforces no braces where they
can be omitted.

E.g.:

    const a = [1, 2, 3].map(x => 2 * x);

over:

    const a = [1, 2, 3].map(x => { return 2 * x; });

#### [generator-star-spacing](http://eslint.org/docs/rules/generator-star-spacing)
Generator are special functions indicated by placing an * after the
function keyword. This rule aims to enforce spacing around the * of
generator functions.

E.g.:

    function* generator() {
      yield "44";
      yield "55";
    }

over:

    function *generator() {
      yield "44";
      yield "55";
    }

#### [require-yield](http://eslint.org/docs/rules/require-yield)
This rule generates warnings for generator functions that do not have
the yield keyword.

## Documentation Comments in JavaScript

We use [JSDoc](http://usejsdoc.org/) to add documentation comments in JavaScript. Please read about it if you haven't used it before.

There are JSDoc plugins available for some of the poplar IDEs/editors to make it easier write documentation comments:

* Sublime Text - [sublime-jsdocs](https://github.com/spadgos/sublime-jsdocs)
* Intellij/WebStorm - [Built in](https://www.jetbrains.com/help/webstorm/2016.2/creating-jsdoc-comments.html)
* Atom - [docblockr](https://atom.io/packages/docblockr)
