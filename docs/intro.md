# Introduction

XPath-parser is a utillity library for parsing and translating XPath-Selectors to different XML-libraries in a vendor indipedendent fashion.

## Usage

```js
var Parser = require("xpath-parser");
var xpath = "//div[@id='identifier']";

console.log(JSON.stringify(Parser.parse(xpath), null, 2));
```

Running this example with nodejs:
```
$> node index.js
```

Should result in the following output:

```json
[
  {
    "seperator": "/",
    "axis": "descendant-or-self",
    "nc": "node",
    "q": "node",
    "args": [
      {}
    ]
  },
  {
    "seperator": "/",
    "axis": "child",
    "nc": "div",
    "q": "div",
    "predicate": [
      [
        {
          "operator": "=",
          "args": [
            [
              {
                "axis": "attribute",
                "nc": "id",
                "q": "id"
              }
            ],
            [
              {
                "value": "identifier"
              }
            ]
          ]
        }
      ]
    ]
  }
]
```

The output disassembled the XPath and created an array of Objects that describe the XPath.

As first element in the array we have the seperator indicating the start of the XPath selector:

```
  {
    "seperator": "/",
    "axis": "descendant-or-self",
    "nc": "node",
    "q": "node",
    "args": [
      {}
    ]
  }
```

As you can see the object denotes the seperator `/` and the axis upon which it operates
(`descendant-or-self`) where `descendant-or-self` is one of 13 axis defined in the XPath
Standard.

[You can read more about `descendant-or-self` and other axis at Mozilla Developer Network.](https://developer.mozilla.org/en-US/docs/Web/XPath/Axes)

Since we gave the first slash no arguments to work with and no predicates to look at, the
value for `args` is an array with one empty object. 

Next is the second slash and it's predicates:

```
  {
    "seperator": "/",
    "axis": "child",
    "nc": "div",
    "q": "div",
    "predicate": [ ... ]
  }
```

Let's concentrate on the first part for now. The parsed XPath's second object denotes first
that it is a new seperator (`/`) and as it came after the first `/` that it is now looking
for elements on the child axis. Together with the fact that _node classifier_ (`nc`) is set
to `div` that we are looking for a `<div>` element somewhere underneath. However(!) this
element has to match a specific characteristic that we are defining in the `predicate` keys
value which is also an array.

```
[
        {
          "operator": "=",
          "args": [
            [
              {
                "axis": "attribute",
                "nc": "id",
                "q": "id"
              }
            ],
            [
              {
                "value": "identifier"
              }
            ]
          ]
        }
      ]
```

Since predicates can be plentiful with their own sub constelations (see for example the usage
of [not](https://msdn.microsoft.com/en-us/library/ms256086(v=vs.110).aspx#CodeSnippetContainerCode_1c7b860c-f736-4adb-b103-4f56914d6a72) or
[contains](http://www.abodeqa.com/2013/01/29/contains-and-starts-with-function-in-xpath/))
those are arrays in arrays. Where in our case the first element denotes that it is an equality
operator(`=`) with 2 arguments (`args` length is 2). Here the first argument describes the axis
it is operating on `attribute` and the second argument describes it's value
(`"value": "identifier"`). Stringing this together we can deduce that we're looking for an attribute
by the name of `id` with the value `identifier`. Since this is the only thing we're looking for in
the XPath this is all we have at this point.

