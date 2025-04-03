## Functions

- Functions should be really short and do one thing (do what the function name says it is doing)
- One level of abstraction per function
	- The same function should not be doing something high level like `getHtml()`, intermediate level `String pagePathName = PathParser.render(pagePath);` and low-level `.append("\n")`
- Ideally, less arguments are better to understand and to test.
	- Ordering of arguments can be confusing
- Don't pass in a boolean as argument since the function is doing multiple things (if flag=true, do something, else do something else)
- Argument lists can be create like
	`void moad(Integer... args);`
	`void dyad(string name, Integer... args);`
- Side effects
	- function promises to do one thing, but does other hidden things
	- avoid these, try to make naming clear and make each function do one thing
- Output arguments
	- Should not pass in an output as argument to a function
		`public void appendFooter(StringBuffer report)`
		`appendFooter(s)`

		Instead, have a function change the state of an object it owns
		`report.appendFooter`
- Functions should either change the state of an object or return some info about that object, not both
	- For example, `set()` should not return something... instead, we can separate out into:
		`if (attributeExists("username")) {
			setAttribute("username", "bob");
		}`
- Use Exceptions over returning error codes so that errors can be processed separately from the happy path code. Otherwise, we may end up with nested if-statements where if- path is the happy path and else- path would be the error handling.
- When using try-catch statements, extract the body of the try and catch blocks into functions of their own
