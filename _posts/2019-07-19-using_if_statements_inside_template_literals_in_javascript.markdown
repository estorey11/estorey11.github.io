---
layout: post
title:      "Using if statements inside template literals in Javascript"
date:       2019-07-19 19:19:54 +0000
permalink:  using_if_statements_inside_template_literals_in_javascript
---


Suppose you have a string, and you want to conditionally add something inside of that string inside a template literal, like so:
```
let price=175;
console.log(`I can${ if(price>100){return "'t"} } afford to buy this!`)
```
This will return `Uncaught SyntaxError: Unexpected token if,` as a regular if statement will not work inside of a template literal. Fortunately, there are a few workarounds to deal with this. The first is to use a ternary operator:
```
console.log(`I can${ price>100 ? "'t" : "" } afford to buy this!`)
// expected output: I can't afford to buy this!
```
This works, but it requires a return value for a false condition, in this case an empty string. If you wanted to avoid this, or perhaps your conditional requires an else if, then you can wrap the if statement in a function and then call that function, all inside of the template literal.
```
console.log(`I can${ ( () => { if ( price > 100 ) { return "'t" } } ) () } afford to buy this!`)
// expected output: I can't afford to buy this!
```
This option is a little less pretty, but allows for much more flexibility.
