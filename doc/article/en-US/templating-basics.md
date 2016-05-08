---
{
  "name": "Templating: Basics",
  "culture": "en-US",
  "description": "A basic guide to using Aurelia's templating engine.",
  "engines" : { "aurelia-doc" : "^1.0.0" },
  "author": {
   	"name": "Scott Jackson",
	"url": "http://www.scottmmjackson.com"
  },
  "contributors": [],
  "translators": [],
  "keywords": ["JavaScript", "Templating", "Custom Attributes"]
}
---

## [Introduction](aurelia-doc://section/1/version/1.0.0)

Aurelia's templating system is simple to learn, and yet powerful enough to build even the most complex applications.
This article will walk you through the construction of a static template, importing that template into parent
templates, binding and manipulating data through the view-model, and the use of conditionals, repeaters, and events.

## [A Simple Template](aurelia-doc://section/2/version/1.0.0)

All Aurelia templates must be wrapped with a `<template>` element. The most basic template is a component that prints
"Hello World":

<code-listing heading="hello-world.html">
  <source-code lang="HTML">
    <template>
      <p>
    	  Hello, World!
      </p>
    </template>
  </source-code>
</code-listing>

This template could be a page in an Aurelia application, or it could be the view for a custom element. A template
containing only boilerplate could be useful as a footer containing copyright information or disclaimers, but where's
the fun in a static template?

All Aurelia templates work with a view-model, so let's create one:

<code-listing heading="hello${context.language.fileExtension}">
  <source-code lang="ES2015/ES2016/TypeScript">
    export class Hello {
      constructor() {
        this.name = 'John Doe';
      }
    }
  </source-code>
</code-listing>

Let's bind the `name` property in our view-model into our template using Aurelia's string interpolation:

<code-listing heading="hello.html">
  <source-code lang="HTML">
    <template>
      <p>
    	  Hello, ${name}!
      </p>
    </template>
  </source-code>
</code-listing>

One of the key features of Aurelia's templating system is helping to reduce context switching between your javascript
code and your template markup. String interpolation using the `${}` operator is a new feature in ES2015 that makes it
 simple to insert values into a string. Thus, Aurelia uses this standard syntax in your templates.

When this template is run, Aurelia will insert the value of the `name` property into the template where `${name}`
appears. Pretty simple, right? But what if we want logic in our string interpolation. Can we add our own expressions?
 Absolutely!

<code-listing heading="greeter.html">
  <source-code lang="HTML">
    <template>
      <p>
    	  ${arriving ? 'Hello' : 'Goodbye'}, ${name}!
      </p>
    </template>
  </source-code>
</code-listing>

<code-listing heading="greeter${context.language.fileExtension}">
  <source-code lang="ES2015/ES2016/TypeScript">
    export class Greeter {
      constructor() {
        this.name = 'John Doe';
        this.arriving = true;
        setTimeout(
          () => this.arriving = false,
          5000);
      }
    }
  </source-code>
</code-listing>

In our template, when `arriving` is true, the ternary operator makes our result `'Hello'`, but when it's false, it
makes our result `'Goodbye'`. Our view-model code initializes `arriving` to `true` and changes it to `false` after 5
seconds (5000 milliseconds). So when we run the template, it will say "Hello, John Doe!" and after 5 seconds, it will
 say "Goodbye, John Doe!". Aurelia re-evaluates the string interpolation when the value of `arriving` changes!

But don't worry, there is no dirty-checking. Aurelia uses an observable-based binding system that reacts to changes
as they happen without having to do dirty-checking. This means that Aurelia doesn't slow down as you add more complex
 functionality to your template and view-model.

## [Binding](aurelia-doc://section/4/version/1.0.0)

Binding in Aurelia allows data from the view-model to drive template behavior. The most basic example of binding
is linking a text box to the view model using `value.bind`. What if we let the user decide who they want to greet,
and whether to say Hello or Goodbye?

<code-listing heading="greeter.html">
  <source-code lang="HTML">
    <template>
      <label for="nameField">
        Who to greet?
      </label>
      <input type="text" value.bind="name" id="nameField">
      <label for="arrivingBox">
        Arriving?
      </label>
      <input type="checkbox" checked.bind="arriving" id="arrivingBox">
      <p>
    	  ${arriving ? 'Hello' : 'Goodbye'}, ${name}!
      </p>
    </template>
  </source-code>
</code-listing>

<code-listing heading="greeter${context.language.fileExtension}">
  <source-code lang="ES2015/ES2016/TypeScript">
    export class Greeter {
      constructor() {
        this.name = 'John Doe';
        this.arriving = true;
      }
    }
  </source-code>
</code-listing>

Above, we have a text box that asks for the name of the person to greet, and a checkbox indicating whether they're
arriving. Because we defined `name` as "John Doe" in our view-model, the initial value of the text-box will be "John
Doe", and with `arriving` set to `true`, our checkbox will start checked. When we change the name, the person we're
greeting will change with it: "Hello, Jane Doe!". If we uncheck the box, the greeting will change: "Goodbye, Jane Doe!"

Notice that the way we set up the binding was by using `value.bind` and `checked.bind`. That `.` within the attribute
 is important: whenever you see the `.`, Aurelia is going to do something with that attribute. The most important
 thing to take away from this section is understanding that Aurelia will link attributes to the left of the `.` with
 actions to the right of the `.`.

Here, we have used the `bind` command to bind the text box `value` attribute to the `name` property, and the
checkbox's `checked` attribute to the `arriving` property. The `bind` command will use the default binding direction,
 which is determined by Aurelia's convention for the attribute and element it's acting on. Simply put, the convention
  is this:

1. Can the user update it? It's a two-way binding.

   Examples: Form elements, and the text content of elements with `content-editable` set to true.

2. Is it anything else? It's a one-way binding.

But what if we want to be explicit about our binding direction?

### Explicit One-way, Two-way, and One-time Binding

Sometimes, we will want data to be solely initialized from the view-model using `one-time`. Other times, we may want
changes in the template to drive changes in the view-model, but not the other way around, using `one-way`. If we want
 the template and the view-model to be kept in sync like we saw previously, we want to use `two-way`.

<code-listing heading="bind-directions.html">
  <source-code lang="HTML">
    <template>
      <p>
        <input type="text" value.one-way="name" />
      </p>
      <p>
        <input type="text" value.two-way="name" />
      </p>
      <p>
        <input type="text" value.one-time="name" />
      </p>
    </template>
  </source-code>
</code-listing>

<code-listing heading="bind-directions${context.language.fileExtension}">
  <source-code lang="ES2015/ES2016/TypeScript">
    export class Hello {
      constructor() {
        this.name = 'John Doe';
      }

    }
  </source-code>
</code-listing>

We have three text boxes that we bind to `name` in different ways. They all start out set to `John Doe`. If we edit
the first, `one-way` bound field, nothing changes. If we edit the second, `two-way` bound field only the first field
changes with it. If we edit the third, `one-time` bound field, nothing else changes.

What's happening here?

1. In the `one-way` bound field, the field is only a **receiver** of changes from the view-model. Changes in the
`one-way` field don't affect the value of `name`, but changes in `name` affect the `value` property of the field.

2. In the `two-way` bound field, the field and the view-model are kept in sync. Changes in the `two-way` field update
 the value of `name`, and changes in `name` affect the field's `value` property as well.

3. In the `one-time` bound field, the field is filled with the initial value of `name` from the view-model only.
Changes in the value of `name` in the view model don't affect the `value` property of the field, and changes in the
field don't affect the value of `name`.

Here's an easy way to remember it: `two-way` is synced, `one-way` only receives, and `one-time` only initializes.

Binding seems like a pretty useful way to connect our view-model to our template markup, but in a large application,
we want to be able to use the same template multiple times with different data. How do we do that?

## [Importing Templates as Custom Elements](aurelia-doc://section/3/version/1.0.0)

The templates we've made so far can also be called "Custom Elements", because when we import them into other
templates, they will take the form of an element in the markup. The first thing to do is to use the `<require>`
element to tell Aurelia where to look for our custom element's definition. Then, the custom element can be inserted
into the template as if it were any other HTML tag!

Here's a pure HTML example:

<code-listing heading="importing-hello.html">
  <source-code lang="HTML">
    <template>
      <require from="./say-hello.html"></require>
      <say-hello></say-hello>
    </template>
  </source-code>
</code-listing>

This is as simple as it gets. We require the html file, and we consume it with a tag whose name is the name of the file.
 However, our "Hello World" could also be a combination of HTML and view-model code:

<code-listing heading="say-hello-vm.html">
  <source-code lang="HTML">
    <template>
      <div>
       ${greeting}
      </div>
    </template>
  </source-code>
</code-listing>
<code-listing heading="say-hello-vm${context.language.fileExtension}">
  <source-code lang="ES2015/ES2016/TypeScript">
  export class SayHello {
    constructor() {
      this.greeting = "Hello, World!"
    }
  }
  </source-code>
</code-listing>

In this case, we would import our template without the `.html` extension:

<code-listing heading="importing-hello-vm.html">
  <source-code lang="HTML">
    <template>
      <require from="./say-hello"></require>
      <say-hello></say-hello>
    </template>
  </source-code>
</code-listing>

Remember to only add the `.html` extension when you're using a custom element that's only an HTML template.

### Binding to a Custom Element

We can also define our own properties on custom elements, and use them to bind data *into* the template.

If the template is pure HTML, we use the `bindable` property in the `template` tag. If the template has view-model
code, we have to import the `bindable` decorator from the `aurelia-framework` package.

First, the pure HTML version:

<code-listing heading="say-hello.html">
  <source-code lang="HTML">
    <template bindable="greeting, whom">
      <div>
    	${greeting || "Hello"}, ${whom || "World"}!
      </div>
    </template>
  </source-code>
</code-listing>

<code-listing heading="binding.html">
  <source-code lang="HTML">
    <template>
      <require from="./say-hello.html"></require>
      <input type="text" value.bind="greetMessage" />
      <input type="text" value.bind="whomMessage" />
      <say-hello greeting.bind="greetMessage" whom.bind="whomMessage"></say-hello>
      <say-hello greeting="Hello" whom="Yao Ming"></say-hello>
      <say-hello></say-hello>
    </template>
  </source-code>
</code-listing>

In this example, we have three copies of the same custom element that do different things to the bindable template:

1. The first binds data from the text boxes in `binding.html`. Our default message is "Hello, World!", but changing
the input fields will update the message based on the content of our textboxes.

2. The second sets the attributes directly without using Aurelia's binding behavior, and gives us a fixed greeting to
 our favorite basketball player.

3. The third leaves the attributes undefined. Our string interpolation `${}` uses the "OR" operator `||` to set a
default message if the attributes are undefined. When we say `${greeting || "Hello"}`, we mean "Use the value of
`greeting` unless it's undefined, in which case use the string 'Hello'." So our result is "Hello, World!"

Custom elements with a view-model defined look slightly different: instead of defining the `bindable` property, we
use the `bindable` decorator, as shown below.

<code-listing heading="say-hello.html">
  <source-code lang="HTML">
    <template>
      <div>
    	${greeting || "Hello"}, ${whom || "World"}!
      </div>
    </template>
  </source-code>
</code-listing>

<code-listing heading="say-hello${context.language.fileExtension}">
  <source-code lang="ES2015/ES2016/TypeScript">
  import {bindable} from 'aurelia-framework';

  export class SayHello {
    @bindable greeting;
    @bindable whom;
  }
  </source-code>
</code-listing>

<code-listing heading="binding-template.html">
  <source-code lang="HTML">
    <template>
      <require from="bindable-dynamic-template"></require>
      <input type="text" value.bind="greetMessage" />
      <input type="text" value.bind="whomMessage" />
      <say-hello greeting.bind="greetMessage" whom.bind="whomMessage">
    </template>
  </source-code>
</code-listing>

Here, the view-model declares `greeting` and `whom` as bindable. This has the same effect as defining `bindable` in
the template markup, except when we define a view-model, the use of the `bindable` decorator is **mandatory**.

As long as the inputs are blank, it displays "Hello, World!", but if we change our first field to "Goodbye", the
message is now "Goodbye, World!" That's too dark for this tutorial, so let's see off our friend Frank instead by
writing "Frank" in the second text field: "Goodbye, Frank!" is printed.

With external binding, you're really given the opportunity to abstract your views and data displays from the form
controls that may be used to manipulate them. Combining the `bindable` property with strong abstractions in the
view-model means that you can write one template and reuse it over and over in your application for maximum
consistency, testability, and reliability.

## [Conditionals](aurelia-doc://section/5/version/1.0.0)

Aurelia has two major tools for conditional display: `if`, and `show`. The difference is that `if` removes the element
entirely from the DOM, and `show` adds the `aurelia-hide` CSS class. What this means from a practical perspective is
that if you're not using Aurelia's CSS, `show` isn't going to do anything unless you define the `aurelia-hide` class
in your css code.

The difference is subtle but important in terms of speed and usability. When the state changes in `if`, the template
and all of its children are deleted from the DOM, which is computationally expensive if it's being done over and over.
However, if `show` is being used for a very large template, such as a dashboard containing thousands of elements with
their own bound data, then keeping those elements loaded-but-hidden may not end up being a useful approach.

Here's our basic "Hello World" that asks the user if they want to greet the world first:

<code-listing heading="if-template.html">
  <source-code lang="HTML">
    <template>
      <label for="greet">Would you like me to greet the world?</label>
      <input type="checkbox" id="greet" checked.bind="greet" />
      <div if.bind="greet">
        Hello, World!
      </div>
    </template>
  </source-code>
</code-listing>

However, if we just want to hide the element from view instead of destroying it completely, we should use `show`
instead of `if`.

<code-listing heading="show-template.html">
  <source-code lang="HTML">
    <template>
      <label for="greet">Would you like me to greet the world?</label>
      <input type="checkbox" id="greet" checked.bind="greet" />
      <div show.bind="greet">
        Hello, World!
      </div>
    </template>
  </source-code>
</code-listing>

When unchecked, our "Hello World" div will have the `aurelia-hide` class, which sets `display: none` if you're using
Aurelia's CSS libraries. However, if you don't want to do that, you can also define your own CSS rules that treat
`aurelia-hide` differently, like setting `visibility: none` or `height: 0px`.

Conditionals can also be one-time bound, so that parts of the template are fixed when they're instantiated:

<code-listing heading="conditional-one-time-template.html">
  <source-code lang="HTML">
    <template>
      <div if.one-time="greet">
        Hello, World!
      </div>
      <div if.one-time="!greet">
        Some other time.
      </div>
    </template>
  </source-code>
</code-listing>

<code-listing heading="bind-template${context.language.fileExtension}">
  <source-code lang="ES2015/ES2016">
    export class ConditionalOneTimeTemplate {
      greet = (Math.random() > 0.5);
    }
  </source-code>
  <source-code lang="Typescript">
    export class ConditionalOneTimeTemplate {
      greet = (Math.random() > 0.5);
    }
  </source-code>
</code-listing>

There's a 50-50 chance that we'll greet the world, or put it off until later. Once the page loads, this is fixed,
because the data is one-time bound. Why don't we use `show.one-time`? If we think about what `show` does, it doesn't
really make sense. We're saying we want a CSS class to be applied that will hide an element, and that it will never
change. In most cases, we want `if` to refuse to create an element we'll never use.

## [Repeaters](aurelia-doc://section/6/version/1.0.0)

Aurelia is also able to repeat elements for each element in an array.

<code-listing heading="repeater-template.html">
  <source-code lang="HTML">
    <template>
      <label for="friendName">Enter a Friend</label>
      <input type="text" id="friendName" value.bind="friendName" />
      <button click.delegate="addFriend()">Add Friend</button>
      <p repeat.for="friend of friends">Hello, ${friend}!</p>
    </template>
  </source-code>
</code-listing>

<code-listing heading="repeater-template${context.language.fileExtension}">
  <source-code lang="ES2015/ES2016">
  export class RepeaterTemplate {
    friends = [];

    addFriend() {
      this.friends.push(this.friendName)
    }
  }
  </source-code>
  <source-code lang="TypeScript">
  export class RepeaterTemplate {
    friends = [];

    addFriend() {
      this.friends.push(this.friendName)
    }
  }
  </source-code>
</code-listing>

This allows me to list out my friends and greet them one by one, rather than attempting to greet all 7 billion
inhabitants of the world at once.

However, let's say that I don't want to greet Alice or Bob, because of their constant cryptic bickering. And perhaps I
only want to greet 10 friends at most. Aurelia has a tool called Value Converters that comes in useful in this case:

<code-listing heading="value-converter-repeater-template.html">
  <source-code lang="HTML">
    <template>
      <require from="./enemies"></require>
      <require from="./take"
      <label for="friendName">Enter a Friend</label>
      <input type="text" id="friendName" value.bind="friendName" />
      <button click.delegate="addFriend()">Add Friend</button>
      <p repeat.for="friend of friends | removeEnemies:alice:bob | take:10">Hello, ${friend}!</p>
    </template>
  </source-code>
</code-listing>

<code-listing heading="value-converter-repeater-template${context.language.fileExtension}">
  <source-code lang="ES2015/ES2016">
  export class ValueConverterRepeaterTemplate {
    friends = [];

    addFriend() {
      this.friends.push(this.friendName)
    }
  }
  </source-code>
  <source-code lang="TypeScript">
  export class ValueConverterRepeaterTemplate {
    friends = [];

    addFriend() {
      this.friends.push(this.friendName)
    }
  }
  </source-code>
</code-listing>

<code-listing heading="enemies${context.language.fileExtension}">
  <source-code lang="ES2015/ES2016">
  export class RemoveEnemiesValueConverter {
    toView(array, ...enemies) {
      // filter the array
      return array.filter((value) =>
        // only those not in my list of enemies can stay.
        enemies.indexOf(value.toLowerCase()) == -1
      )
    }
  }
  </source-code>
  <source-code lang="TypeScript">
  export class RemoveEnemiesValueConverter {
    toView(array, ...enemies) {
      // filter the array
      return array.filter((value) =>
        // only those not in my list of enemies can stay.
        enemies.indexOf(value.toLowerCase()) == -1
      );
    }
  }
  </source-code>
</code-listing>
<code-listing heading="take${context.language.fileExtension}">
  <source-code lang="ES2015/ES2016">
  export class TakeValueConverter {
    toView(array, number) {
      return array.slice(0,number);
    }
  }
  </source-code>
  <source-code lang="TypeScript">
  export class TakeValueConverter {
    toView(array, number) {
      return array.slice(0,number);
    }
  }
  </source-code>
</code-listing>

In this example, we define two ValueConverters, one that filters out my enemies, and one that limits us to 10 repeats.
ValueConverters are classes that require three things:

1. Class name must end in ValueConverter

1. Class name is PascalCase, but in an HTML template it will be camelCase. For example: `RemoveEnemiesValueConverter`
   becomes `removeEnemies`.

1. Must define a function `toView`

1. First argument is input located to the left of the pipe character, `|`

1. Other arguments are delimited by colons to the right of the name

Thus, when we have a `toView` method that takes any number of enemies, we might be able to say
`friends | removeEnemies:bob:alice:timothy:eunice`, if we're making a lot of enemies.

## [Event Binding](aurelia-doc://section/7/version/1.0.0)

Aurelia's event binding simplifies handling events like user interaction. Above, the `.delegate` event binding strategy
has been used twice: once as `submit.delegate` on a `<form>` element, and once as `click.delegate` on a `<button>`
element.

There is another option for event handling: `.trigger`. `.trigger` works similar to `.delegate`, but attaches the event
directly to the element rather than the app itself. This is slightly confusing, but the short answer is that `.delegate`
won't work for events that don't bubble up through parent elements, such as some custom events or on iOS Safari. In
these cases, you'll want to use `.trigger`. However, `.trigger` is much more expensive in memory, and will slow your
page down considerably, so the general guidance is to use `.delegate` unless you absolutely must use `.trigger`.

<code-listing heading="event-template.html">
  <source-code lang="HTML">
    <template>
      <button click.delegate="leanEventHandler">Do something nimbly!</button>
      <button click.trigger="expensiveEventHandler">Do something requiring more memory...</button>
    </template>
  </source-code>
</code-listing>

<code-listing heading="event-template${context.language.fileExtension}">
  <source-code lang="ES2015/ES2016">
  export class EventTemplate {
    leanEventHandler(event) {
      // do nothing
    }
    expensiveEventHandler(event) {
      // do nothing
    }
  }
  </source-code>
  <source-code lang="TypeScript">
  export class EventTemplate {
    leanEventHandler(event) {
      // do nothing
    }
    expensiveEventHandler(event) {
      // do nothing
    }
  }
  </source-code>
</code-listing>

In the above example, `leanEventHandler` and `expensiveEventHandler` both do nothing. However,
`expensiveEventHandler` will be more expensive, because the handler is being attached with `.trigger`.

Another
