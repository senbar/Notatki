
## JSX
JSX= syntax extension to JS.
 It also allows React to show more useful error and warning messages.
```js
const element = <h1>Hello, world!</h1>;var z=1
```

JSX jest normalna ekspresja w js i mozna go uzywac w funkcjach itp 
```js
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```
Don’t put quotes around curly braces when embedding a JavaScript expression in an attribute. You should either use quotes (for string values) or curly braces (for expressions), but not both.

Important!
Since JSX is closer to JavaScript than to HTML, React DOM uses camelCase property naming convention instead of HTML attribute names.

JSX Prevents Injection Attacks
It is safe to embed user input in JSX

Injection attack-https://stackoverflow.com/questions/7381974/which-characters-need-to-be-escaped-in-html
React escapes dom elements- zamienia znaki interpretowane w htmlu jako keywordy na ich numery unicodeowe

Babel compiles JSX down to React.createElement() calls.

```js
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
// compiles to 
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```