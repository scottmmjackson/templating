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

Aurelia's powerful templating system can seem daunting in its simplicity. This article will walk you through the
construction of a static template, importing that template into parent templates, binding and manipulating data through
the view-model, and the use of conditionals, repeaters, and events.

## [The Static Template](aurelia-doc://section/2/version/1.0.0)

The core of an Aurelia custom element is the template HTML file. All Aurelia custom elements are composed at least
of HTML wrapped in a `<template>` element, but may also include an explicit view-model definition, which we'll discuss
later.

The most basic template is a component that prints "Hello World":

<code-listing heading="hello-template.html">
  <source-code lang="HTML">
    <template>
      <div>
    	  Hello, World!
      </div>
    </template>
  </source-code>
</code-listing>

This template can be imported into an Aurelia application, or consumed in another template. A static template has its
uses: a boilerplate footer containing copyright information or disclaimers, but for many of our purposes, we'll want
to involve some more dynamic features, such as repeaters, conditionals, event handlers, and data binding.

## [Imports](aurelia-doc://section/3/version/1.0.0)

There are two ways to consume templates, depending on whether they're pure HTML templates, or HTML with a script engine
view-model.

Here's a pure HTML example:

<code-listing heading="consuming-hello-template.html">
  <source-code lang="HTML">
    <template>
      <require from="./hello-template.html"></require>
      <hello-template></hello-template>
    </template>
  </source-code>
</code-listing>

This is as simple as it gets. We require the html file, and we consume it with a tag whose name is the name of the file.
 However, our "Hello World" could also be a combination of HTML and view-model code:

<code-listing heading="hello-vm-template.html">
  <source-code lang="HTML">
    <template>
      <div>
       ${greeting}
      </div>
    </template>
  </source-code>
</code-listing>
<code-listing heading="hello-vm-template${context.language.fileExtension}">
  <source-code lang="ES2015/ES2016">
  export class HelloDynamicTemplate {
    greeting = "Hello, World!"
  }
  </source-code>
  <source-code lang="TypeScript">
  export class HelloDynamicTemplate {
    greeting = "Hello, World!"
  }
  </source-code>
</code-listing>

In this case, we would import our template without the `.html` extension:

<code-listing heading="consuming-hello-vm-template.html">
  <source-code lang="HTML">
    <template>
      <require from="./hello-template"></require>
      <hello-template></hello-template>
    </template>
  </source-code>
</code-listing>

In this case, we're consuming the HTML file, but we're also consuming its view-model definition. Remember to only add
the `.html` extension when you're using a custom element that's only an HTML template.

## [Binding](aurelia-doc://section/4/version/1.0.0)

Binding in Aurelia allows data from the script engine to drive template behavior. The most basic example of binding
is linking a text box to the view model using `value.bind`, and displaying the bound data using string interpolation,
 the `${}` operator:


<code-listing heading="bind-template.html">
  <source-code lang="HTML">
    <template>
        <label for="name">
          Name:
        </label>
        <input type="text" value.bind="myName" id="name" />
        <button click.delegate="capitalizeIt()">
          Capitalize It!
        </button>
        <br />
        <p>
          Hi, my name is ${myName}!
        </p
      </form>
    </template>
  </source-code>
</code-listing>

<code-listing heading="bind-template${context.language.fileExtension}">
  <source-code lang="ES2015/ES2016">
    export class BindTemplate {
	name = 'Dana Lee';

	capitalizeName() {
	  this.myName = this.myName.toUpperCase();;
}
    }
  </source-code>
  <source-code lang="Typescript">
    export class BindTemplate {
        name = 'Dana Lee';

	capitalizeName() {
	  this.myName = this.myName.toUpperCase();
	}
    }
  </source-code>
</code-listing>

In this example, the value of the text field is **two-way bound** to `BindTemplate.name`. That means that upon
altering the value of `name`, and the contents of the text field are kept in sync and vice versa. Because we've hooked
up the capitalize function, we can call that function, which alters the value of `name` and changes the value of the
`<input>` field to the uppercase version of itself.

### One-way, Two-way, and One-time Binding

We don't always want to keep a field in sync with our model. Sometimes, we will want data to be solely initialized
from the view-model using `one-time`. Other times, we may want changes in the template to drive changes in the
view-model, but not the other way around, using `one-way`. If we want the template and the view-model to be kept in
sync like we saw previously, we want to use `two-way`. Using `bind` picks the default binding behavior for the
element, which is often `two-way`.

<code-listing heading="bind-directions.html">
  <source-code lang="HTML">
    <template>
      <p>
        <button click.delegate="capitalizeOnce()">
          Capitalize Me!
        </button>
        <input type="text" value.one-time="once" />
        ${once}
      </p>
      <p>
        <button click.delegate="capitalizeSender()">
          Capitalize Me!
        </button>
        <input type="text" value.one-way="sender" />
        ${sender}
      </p>
      <p>
        <button click.delegate="capitalizeCommunicator()">
          Capitalize Me!
        </button>
        <input type="text" value.two-way="communicator" />
        ${communicator}
      </p>
    </template>
  </source-code>
</code-listing>

<code-listing heading="bind-directions${context.language.fileExtension}">
  <source-code lang="ES2015/ES2016">
    export class BindDirections {
      once = 'First';
      sender = 'Second';
      communicator = 'Third';

      capitalizeOnce() {
        this.once = this.once.toUpperCase();
      }

      capitalizeSender() {
        this.sender = this.sender.toUpperCase();
      }

      capitalizeCommunicator() {
        this.communicator = this.communicator.toUpperCase();
      }

    }
  </source-code>
  <source-code lang="TypeScript">
    export class BindDirections {
      once = 'First';
      sender = 'Second';
      communicator = 'Third';

      capitalizeOnce() {
        this.once = this.once.toUpperCase();
      }

      capitalizeSender() {
        this.sender = this.sender.toUpperCase();
      }

      capitalizeCommunicator() {
        this.communicator = this.communicator.toUpperCase();
      }

    }
  </source-code>
</code-listing>

Each of the checkboxes triggers the functions they're bound to when checked. Much like the `capitalizeName` function
above, they convert the bound data to upper-case. However, because the binding strategies are different, we get
different results:

1. When the first one-time bound text field is altered, `once` doesn't change. When the box is checked, `once` becomes
   "FIRST", all upper-case, but the text field doesn't change.

1. When the second one-way bound text field is altered, `sender` changes. When the box is checked, `sender` becomes
   the all-caps value of the text field, but the text field doesn't change. Thus, the one-way bound text field is a
   sender, but not a receiver.

1. When the third two-way bound text field is altered, `communicator` changes. When the box is checked, `communicator`
   becomes the all-caps value of the text field, and the text field is updated to reflect that change. Thus, the
   two-way bound text field is both a sender and a receiver.

In this case, if we had used `.bind` it would have defaulted to two-way binding. However, depending on the nature of
the action, Aurelia will infer the binding type from the context: the property and element on which the `bind` call
is made.

What if I want something that is only a receiver and not a sender? Most times, this can be achieved by using the
string  interpolation operator: `${}`. For example, in the above code, we're using string interpolation to inspect the
values of `once`, `sender`, and `communicator`. Those interpolated strings won't have any effect on the values of
those variables.

### Binding to a Custom Element

Aurelia can also export and import its own bindables. This is done differently depending on whether or not the template
is pure HTML, or if it contains view-model code. If the template is pure HTML, we use the `bindable` property in the
`template` tag. If the template has view-model code, we have to import the `bindable` decorator from the
`aurelia-framework` package.

First, the pure HTML version:

<code-listing heading="bindable-template.html">
  <source-code lang="HTML">
    <template bindable="greeting, whom">
      <div>
    	${greeting || "Hello"}, ${whom || "World"}!
      </div>
    </template>
  </source-code>
</code-listing>

<code-listing heading="binding-template.html">
  <source-code lang="HTML">
    <template>
      <require from="./bindable-template.html"></require>
      <input type="text" value.bind="greetMessage" />
      <input type="text" value.bind="whomMessage" />
      <bindable-template greeting.bind="greetMessage" whom.bind="whomMessage">
    </template>
  </source-code>
</code-listing>

In this example, the `bindable-template` sets the `bindable` property and declares which variables can be bound by its
parent template. In this case, it allows its parent to set `greeting`, and `whom`.

Now, the version using a dynamic template with its own view-model:

<code-listing heading="bindable-dynamic-template.html">
  <source-code lang="HTML">
    <template>
      <div>
    	${greeting || "Hello"}, ${whom || "World"}!
      </div>
    </template>
  </source-code>
</code-listing>

<code-listing heading="bindable-dynamic-template${context.language.fileExtension}">
  <source-code lang="ES2015/ES2016">
  import {bindable} from 'aurelia-framework';

  export class BindableDynamicTemplate {
    @bindable greeting;
    @bindable whom;
  }
  </source-code>
  <source-code lang="TypeScript">
  export class BindableDynamicTemplate {
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
      <bindable-template greeting.bind="greetMessage" whom.bind="whomMessage">
    </template>
  </source-code>
</code-listing>

Here, the view-model declares `greeting` and `whom` as bindable. These two versions are equivalent.

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
