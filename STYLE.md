# Polymer Elements Design Guide


### Decisions to make:

* // Do styles live in a `<style>` element or in a .css file?
* Should we have guidelines around method complexity? That is, what level of complexity must be
  refactored before it can be merged.
  * Subjective call, feedback should be in a review
* How do mixins fit in?
  * Use judiciously
* Prefer native element “wrappers” for elements that already have shadow roots (input, textarea,
  etc.) or mirror native functionality? See `paper-input` vs. `paper-input-wrapper`.
* Limit internal state in elements? Expose all state via properties/attributes/events.
* Require custom attributes that might be applied to native elements be prefixed with `data-`? Ex:
  `core-drawer-panel` looks for its drawer and main sections via drawer and main attributes that can
  be applied to any element. They are invalid HTML when applied to native elements.
* `<!DOCTYPE html>` (uppercase) or `<!doctype html>` (lowercase)?
* `e` or `event` for default argument to event handlers?


## Guidelines


### Custom Elements

* Ideally, everything is a Polymer element. Wrap functionality in Polymer elements.
* Prefer composition of custom elements over extending other custom elements.
  * However, consider extending native elements like `HTMLButtonElement` if the new custom element
    would end up re-implementing a significant amount of native functionality.
* Prefer one-way binding (square brackets `[[ ]]`) by default, use two-way binding (curly brackets
  `{{ }}`) only when needed


### Function/Attribute/Identifier/Event Naming

* Attribute changed handlers end with “Changed”
  * `selectedChanged`, `disabledChanged`, etc.
* Event handlers begin with “on”
  * `onSelect`, `onClose`, etc.
* Identifiers are camelCase
  * `fooBarBaz` instead of `foobarbaz` / `foo_bar_baz` or other variations
* Abbreviations are all upper-case
  * `lightDOM` instead of `lightDom`
  * `newHTML` instead of `newHtml`
* Constants are `ALL_CAPS` with words separated by underscores.
* Events that bubble from a custom element need a dash in the event name
  * `x-foo` fires a bubbling event `bubbling-event`, not `bubblingevent`.


### Imports

* Include all explicit dependencies including Polymer itself if the element calls `Polymer`.
* Ordering at the top of a file:
  * First, by dependency requirement if ordering is significant.
  * Second, group alphabetically by `href`. Optionally add line breaks between components in
    different directories (all “awesome”, linebreak, all “gnarly”)

    ```html
    <link rel="import" href="components/awesome/foo-baz.html">
    <link rel="import" href="components/gnarly/baz-foo.html">
    <link rel="import" href="components/gnarly/foo-baz.html">
    ```


### Modules

* Hide class-private data inside a function scope

  ```js
  (function() {

    var DEFAULT_NAME = "Georgia";

    Polymer({

      is: "x-name",

      ready: function() {
        this.name = DEFAULT_NAME;
      }

    });

  }());
  ```
* **[pending]** Run all JavaScript in ES5 strict mode. For Polymer elements that means wrapping them
  in a function scope and adding the `‘use strict’;` statement.

  ```js
  (function() {

    'use strict';

    ...

  }());
  ```
* Prefix instance-private data with an underscore (“_”). Identifiers prefixed with an underscore
  should not be considered part of an element’s API.

  ```js
  Polymer({

    is: "x-element",

    listener: function() {},

    ready: function() {
      // `_boundListener` is "private". Renaming between
      // between releases is fine.
      this._boundListener = this.listener.bind(this);
    }

  });
  ```


### Repositories

* If an element is meant to be publicly consumed, it should have its own repo under the Polymer org
  by the same name as the element.
* If an element is internal and not meant to be instantiated or part of a public API, it can live in
  whichever repo uses it.


### Styling

* Target CSS selectors with classes
* Don’t target CSS selectors with IDs. Since they are available on this.$, IDs should be considered
  usable in JavaScript only.


### Templates

* `<dom-module id="element-name">` wrapper for templates over a `<template>` preceding a `<script>`
  element

  ```html
  <dom-module id="cat-face">

    <template>
    </template>

  </dom-module>

  <script>

    Polymer({

      is: "cat-face"

    });

  </script>
  ```
* Prefer template (declarative) property binding where possible over setting properties imperatively

  ```html
  <x-foo bar="{{baz}}"></x-foo>
  ```

  instead of

  ```html
  <x-foo id="foo"></x-foo>
    <!-- … -->
  this.$.foo.bar = this.baz;
  ```


### Whitespace / Indentation

* Use single quotes for strings in JavaScript

  ```js
  var blah = 'foo';
  ```
* Use double quotes for attribute values in HTML

  ```html
  <x-blah foo="baz"></x-blah>
  ```
* No line break at the top of files
* 2 spaces for indentation
* Content for every tag except `<head>` and `<body>` is indented one level

  ```html
  <html>

  <head>
    /* something */
  </head>
  <body>
    /* something else */
  </body>

  </html>
  ```

  and

  ```html
  <script>

    (function() {
      Polymer(...);
    })();

  </script>
  ```
* No spaces inside bindings

  ```html
  <div foo="{{bar}}"></div>
  <section bar="[[baz]]"></section>
  ```
* Line breaks between methods in object passed to `Polymer()`

  ```js
  Polymer({

    created: function() {},

    ready: function() {}

  });
  ```
* Line breaks between style blocks

  ```css
  .panel {
    padding: 10px;
  }

  .panel-puffy {
    padding: 20px;
  }
  ```
* Internal leading and trailing line breaks for top-level elements (`<dom-module>`, `<style>`,
  `<script>`, `<html>`, `<head>`, `<body>`)

  ```html
  <dom-module id=”foo-element”>

    <template>
      <h3>Buenos dias</h3>
    </template>

  </dom-module>
  ```
* 100 columns for line breaks
* When breaking a long HTML tag, expand attributes, and indent by 4

  ```html
  <some-element
      foo="really really long value"
      bar="{{bar}}"
      baz="some other really long value">
    <div>Has child elements too</div>
  </some-element>
  ```
* Files end with new lines
* No whitespace at the end of lines


### Function Declarations

* No spaces between `function` keyword or function/method name and argument list
* Opening brace on the same line as the function signature

  ```js
  function(arg1, arg2, arg3) {
    var foo = 1;
  }
  ```
