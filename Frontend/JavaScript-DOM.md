# JavaScript DOM --- Complete Study Notes

> Learn how JavaScript reads and updates HTML through the Document
> Object Model, handles events, manages forms, and builds interactive
> interfaces.

## Table of Contents

-   [1. What Is the DOM?](#1-what-is-the-dom)
-   [2. How HTML Becomes the DOM](#2-how-html-becomes-the-dom)
-   [3. Selecting Elements](#3-selecting-elements)
-   [4. Reading and Updating Content](#4-reading-and-updating-content)
-   [5. Attributes and Properties](#5-attributes-and-properties)
-   [6. Classes and Inline Styles](#6-classes-and-inline-styles)
-   [7. Creating and Removing
    Elements](#7-creating-and-removing-elements)
-   [8. Events](#8-events)
-   [9. Event Object and Event Flow](#9-event-object-and-event-flow)
-   [10. Event Delegation](#10-event-delegation)
-   [11. Forms and Validation](#11-forms-and-validation)
-   [12. Traversing the DOM](#12-traversing-the-dom)
-   [13. Dataset Attributes](#13-dataset-attributes)
-   [14. Timers and DOM Updates](#14-timers-and-dom-updates)
-   [15. DOM Readiness and Script
    Loading](#15-dom-readiness-and-script-loading)
-   [16. Security: XSS and Safe DOM
    Updates](#16-security-xss-and-safe-dom-updates)
-   [17. Performance and Best
    Practices](#17-performance-and-best-practices)
-   [18. Common Mistakes](#18-common-mistakes)
-   [19. Interview Questions](#19-interview-questions)
-   [20. Practice Projects](#20-practice-projects)
-   [21. References](#21-references)

## 1. What Is the DOM?

**DOM (Document Object Model)** is a programming interface representing
an HTML or XML document as a tree of objects called nodes. JavaScript
can use DOM APIs to inspect, create, modify, and remove page content.

HTML defines the initial structure; the DOM is the browser's in-memory
representation. JavaScript changes the DOM, and the browser updates the
rendered page as needed.

The DOM is not the same thing as JavaScript itself. It is a
browser-provided API that JavaScript can access.

## 2. How HTML Becomes the DOM

Example HTML:

``` html
<body>
  <main id="app">
    <h1>Hello</h1>
    <p>Welcome to the page.</p>
  </main>
</body>
```

Simplified tree:

``` text
document
└── html
    ├── head
    └── body
        └── main#app
            ├── h1
            │   └── "Hello"
            └── p
                └── "Welcome to the page."
```

Important node types include: - `Document` - `Element` - `Text` -
`Comment`

`document` is the entry point for many DOM operations. `window`
represents the browser window and exposes other browser APIs.

## 3. Selecting Elements

HTML:

``` html
<h1 id="title">Hello</h1>
<p class="message">Welcome</p>
<button data-action="save">Save</button>
```

### `getElementById`

``` js
const title = document.getElementById("title");
```

Returns the element with that ID, or `null` if not found.

### `querySelector`

``` js
const message = document.querySelector(".message");
const saveButton = document.querySelector('[data-action="save"]');
```

Returns the first matching element, or `null`.

### `querySelectorAll`

``` js
const paragraphs = document.querySelectorAll("p");

paragraphs.forEach((paragraph) => {
  console.log(paragraph.textContent);
});
```

Returns a **static NodeList** of matching elements. It does not
automatically update when new matching elements are added.

### Other selectors

``` js
const items = document.getElementsByClassName("item");
const tags = document.getElementsByTagName("li");
```

These HTML collections are generally live, meaning they can reflect
changes to the document. `querySelectorAll` is often convenient for
modern code.

**Common mistake:** Always account for a selector returning `null` when
the element may not exist.

``` js
const button = document.querySelector("#save");

if (button) {
  button.textContent = "Ready";
}
```

## 4. Reading and Updating Content

HTML:

``` html
<p id="status">Not saved</p>
<div id="description"><strong>Old</strong> description</div>
```

### `textContent`

``` js
const status = document.querySelector("#status");
status.textContent = "Saved successfully";
```

Gets or sets text content without parsing the assigned string as HTML.

### `innerHTML`

``` js
const description = document.querySelector("#description");
description.innerHTML = "<strong>New</strong> description";
```

Reads or parses HTML markup. Setting it replaces the element's
descendants and can introduce security vulnerabilities if the string
contains untrusted input.

### `innerText`

``` js
console.log(status.innerText);
```

Reflects rendered text and is affected by layout and visibility.
`textContent` is generally preferred when you simply need the text
content.

Rule of thumb: - Use `textContent` for plain text. - Use `innerHTML`
only for trusted or properly sanitized markup when HTML parsing is
intended. - Do not use `innerHTML` with raw user input.

## 5. Attributes and Properties

HTML:

``` html
<a id="profile-link" href="/profile" target="_self">Profile</a>
<input id="email" type="email" required>
```

Read an attribute:

``` js
const link = document.querySelector("#profile-link");
console.log(link.getAttribute("href"));
```

Change an attribute:

``` js
link.setAttribute("href", "/account");
link.removeAttribute("target");
```

Properties represent the current DOM object's state:

``` js
const email = document.querySelector("#email");
console.log(email.value);
console.log(email.required);
email.value = "person@example.com";
```

Attributes and properties are related but not identical. For example, an
input's `value` property reflects its current value, while the `value`
attribute usually represents its default value.

For boolean HTML attributes, use properties such as
`input.disabled = true` or `button.hidden = true` where appropriate.

## 6. Classes and Inline Styles

``` html
<div id="panel" class="panel">Panel content</div>
```

### `classList`

``` js
const panel = document.querySelector("#panel");

panel.classList.add("active");
panel.classList.remove("active");
panel.classList.toggle("active");
console.log(panel.classList.contains("active"));
```

Toggle with explicit state:

``` js
panel.classList.toggle("active", true);
```

### Inline styles

``` js
panel.style.backgroundColor = "lavender";
panel.style.padding = "1rem";
```

CSS property names are usually camel-cased in JavaScript, such as
`backgroundColor`.

**Best practice:** Put reusable visual rules in CSS classes and toggle
those classes from JavaScript rather than writing many inline styles.

## 7. Creating and Removing Elements

``` html
<ul id="task-list"></ul>
```

``` js
const list = document.querySelector("#task-list");

const item = document.createElement("li");
item.textContent = "Review SQL notes";
item.classList.add("task");

list.append(item);
```

Other insertion methods: - `append()` --- adds nodes or text at the
end. - `prepend()` --- adds at the beginning. - `before()` / `after()`
--- inserts adjacent to an element. - `replaceWith()` --- replaces an
element. - `remove()` --- removes the element.

### Build a list safely

``` js
const tasks = ["Learn DOM", "Practice events", "Build a project"];
const fragment = document.createDocumentFragment();

for (const task of tasks) {
  const li = document.createElement("li");
  li.textContent = task;
  fragment.append(li);
}

list.append(fragment);
```

A `DocumentFragment` can make multi-element construction clearer and may
reduce intermediate document updates. Modern browsers also optimize many
direct DOM operations, so measure before optimizing.

## 8. Events

An event represents something that happens, such as a click, key press,
input change, or form submission.

HTML:

``` html
<button id="counter-button" type="button">Click me</button>
<p id="count">0</p>
```

JavaScript:

``` js
const button = document.querySelector("#counter-button");
const output = document.querySelector("#count");
let count = 0;

button.addEventListener("click", () => {
  count += 1;
  output.textContent = String(count);
});
```

Common events: - `click` - `input` - `change` - `submit` - `keydown` /
`keyup` - `focus` / `blur` - `pointerdown` / `pointerup` -
`DOMContentLoaded`

Use `addEventListener` rather than inline HTML event attributes for
maintainability.

### Remove a listener

``` js
function handleClick() {
  console.log("Clicked");
}

button.addEventListener("click", handleClick);
button.removeEventListener("click", handleClick);
```

The same function reference and compatible options are needed to remove
a listener. An anonymous function written separately is a different
function object.

## 9. Event Object and Event Flow

``` js
button.addEventListener("click", (event) => {
  console.log(event.type);
  console.log(event.target);
  console.log(event.currentTarget);
});
```

-   `event.target`: the original target of the event, often the deepest
    clicked element.
-   `event.currentTarget`: the element whose listener is currently
    running.
-   `event.preventDefault()`: prevents the browser's default action when
    the event is cancelable.
-   `event.stopPropagation()`: stops further propagation through the
    event path; it does not prevent the default action.

### Bubbling and capturing

Most common UI events bubble from the target toward ancestors. Capturing
travels from ancestors toward the target.

``` js
document.querySelector("#parent").addEventListener(
  "click",
  () => console.log("Capturing listener"),
  { capture: true }
);
```

Use `stopPropagation()` only when necessary because it can interfere
with other listeners and component behavior.

## 10. Event Delegation

Instead of attaching a listener to every child, attach one to a shared
parent and use bubbling.

``` html
<ul id="menu">
  <li><button data-action="edit">Edit</button></li>
  <li><button data-action="delete">Delete</button></li>
</ul>
```

``` js
const menu = document.querySelector("#menu");

menu.addEventListener("click", (event) => {
  const button = event.target.closest("button[data-action]");

  if (!button || !menu.contains(button)) return;

  if (button.dataset.action === "edit") {
    console.log("Edit selected");
  } else if (button.dataset.action === "delete") {
    console.log("Delete selected");
  }
});
```

Benefits: - Fewer event listeners for many child elements. - Works for
matching children added later. - Centralizes handling for related
actions.

Check that the matched element belongs to the intended parent before
acting.

## 11. Forms and Validation

HTML:

``` html
<form id="signup-form">
  <label for="signup-email">Email</label>
  <input id="signup-email" name="email" type="email" required>
  <button type="submit">Sign up</button>
  <p id="form-message" role="status"></p>
</form>
```

``` js
const form = document.querySelector("#signup-form");
const emailInput = document.querySelector("#signup-email");
const message = document.querySelector("#form-message");

form.addEventListener("submit", (event) => {
  event.preventDefault();

  if (!form.checkValidity()) {
    form.reportValidity();
    return;
  }

  message.textContent = `Ready to submit: ${emailInput.value}`;
});
```

-   `preventDefault()` stops normal navigation/submission when
    appropriate.
-   `checkValidity()` checks built-in constraint validation.
-   `reportValidity()` asks the browser to display validation feedback.
-   `input.value` reads a form field's current value.
-   `FormData` can collect submitted controls.

``` js
const formData = new FormData(form);
const email = formData.get("email");
```

Client-side validation improves usability, but server-side validation is
still required. Do not trust client-provided values.

## 12. Traversing the DOM

``` js
const item = document.querySelector(".task");

console.log(item.parentElement);
console.log(item.children);
console.log(item.firstElementChild);
console.log(item.nextElementSibling);
console.log(item.previousElementSibling);
```

Useful APIs: - `parentElement` - `children` - `firstElementChild` -
`lastElementChild` - `nextElementSibling` - `previousElementSibling` -
`closest(selector)`

Prefer `Element`-specific properties when you want elements rather than
text and comment nodes. `childNodes` includes text and comment nodes.

## 13. Dataset Attributes

HTML:

``` html
<button data-user-id="42" data-action="follow">Follow</button>
```

JavaScript:

``` js
const button = document.querySelector("button");

console.log(button.dataset.userId); // "42"
console.log(button.dataset.action); // "follow"

button.dataset.action = "unfollow";
```

Names after `data-` become camel-cased in `dataset`. Values are strings,
so convert numeric values when necessary:

``` js
const userId = Number(button.dataset.userId);
```

Do not treat dataset values as trusted authorization or security
information. The user can change them in DevTools.

## 14. Timers and DOM Updates

``` js
const message = document.querySelector("#message");

const timeoutId = setTimeout(() => {
  message.textContent = "This message appeared later.";
}, 1000);

clearTimeout(timeoutId);
```

Repeat an action:

``` js
const intervalId = setInterval(() => {
  console.log("Still running");
}, 2000);

// Stop when no longer needed:
clearInterval(intervalId);
```

Timers schedule callbacks; they do not guarantee exact execution time.
Avoid intervals that continue after a component or page feature is no
longer needed.

## 15. DOM Readiness and Script Loading

Preferred classic script:

``` html
<script src="/js/app.js" defer></script>
```

`defer` lets the browser parse the HTML before executing the external
classic script. Module scripts are deferred by default.

If code may run before parsing finishes:

``` js
document.addEventListener("DOMContentLoaded", () => {
  const title = document.querySelector("h1");
  console.log(title?.textContent);
});
```

If a script is loaded with `defer` and runs after parsing, a separate
`DOMContentLoaded` listener is often unnecessary. Also note that
`DOMContentLoaded` does not wait for all images and other resources to
finish loading.

## 16. Security: XSS and Safe DOM Updates

**Cross-site scripting (XSS)** can occur when untrusted input is
interpreted as executable markup or script.

Unsafe:

``` js
// Do not insert untrusted user content as HTML.
output.innerHTML = userInput;
```

Safer for plain text:

``` js
output.textContent = userInput;
```

For intentionally supported HTML, use a reputable sanitizer and follow
its documented safe usage. Avoid creating executable event-handler
attributes from strings. Treat URL values and other DOM sinks carefully
as well.

Other practices: - Validate data on the server. - Use
context-appropriate output encoding. - Avoid `eval()` and similar
string-to-code APIs. - Use Content Security Policy as an additional
defense where appropriate. - Do not assume that a value is safe because
it came from a form or a `data-*` attribute.

## 17. Performance and Best Practices

-   Cache frequently used element references when it improves clarity.
-   Avoid querying the entire document repeatedly inside large loops.
-   Use event delegation for large or dynamic lists where appropriate.
-   Batch DOM construction when it improves clarity or measured
    performance.
-   Avoid repeatedly reading layout measurements and writing styles in
    alternating loops; this can trigger layout thrashing.
-   Prefer CSS classes for visual state.
-   Keep event listeners and timers under control.
-   Use `requestAnimationFrame()` for visual updates that need to align
    with browser rendering.
-   Profile real bottlenecks in DevTools before optimizing.
-   Keep accessibility in mind when changing content or focus.

## 18. Common Mistakes

1.  Calling `querySelector()` and assuming it never returns `null`.
2.  Using `innerHTML` with user input.
3.  Forgetting `event.preventDefault()` when intentionally replacing
    form submission.
4.  Confusing `event.target` with `event.currentTarget`.
5.  Expecting `querySelectorAll()` to return a live collection.
6.  Adding listeners inside loops when delegation would be simpler.
7.  Removing a listener with a different anonymous function.
8.  Updating only visual state but not accessibility state such as
    `aria-expanded`.
9.  Assuming browser-side validation makes data trustworthy.
10. Forgetting to clear timers or remove listeners in long-lived
    interfaces.

## 19. Interview Questions

**1. What is the DOM?**\
A tree-like object model of a document exposed through browser APIs.

**2. What is the difference between `querySelector` and
`querySelectorAll`?**\
The first returns the first match or `null`; the second returns a static
NodeList of all matches.

**3. `innerHTML` vs `textContent`?**\
`innerHTML` parses or serializes markup; `textContent` reads or sets
text. `textContent` is safer for untrusted plain text.

**4. What is event bubbling?**\
An event propagates from its target toward ancestor elements.

**5. What is event delegation?**\
Handling events from many descendants through a listener on a common
ancestor.

**6. `preventDefault` vs `stopPropagation`?**\
The first cancels a default browser action where possible; the second
stops propagation along the event path.

**7. What is the difference between an attribute and a property?**\
An attribute is part of markup/element attributes; a property is part of
the live DOM object. Some properties reflect attributes, but their
values can diverge.

**8. What does `DOMContentLoaded` mean?**\
The HTML document has been parsed and deferred scripts have executed; it
does not wait for every resource such as images to load.

**9. Why use `textContent` for user input?**\
It treats the value as text instead of parsing it as HTML, reducing a
common XSS risk.

**10. What is a live collection?**\
A collection that reflects document changes automatically, unlike a
static snapshot such as the NodeList returned by `querySelectorAll()`.

**11. What is `dataset`?**\
An interface for reading and writing an element's `data-*` attributes.

**12. What is the difference between `remove()` and `removeChild()`?**\
`remove()` removes the element itself; `removeChild()` is called on a
parent and takes the child to remove.

## 20. Practice Projects

1.  Counter with increment, decrement, and reset.
2.  To-do list with add, complete, and delete actions.
3.  Signup form with validation and status feedback.
4.  FAQ accordion with keyboard-accessible controls.
5.  Modal dialog with open/close behavior and focus handling.
6.  Searchable list using `input` events.
7.  Dynamic table built safely from an array of objects.
8.  Theme toggle using CSS classes and accessible state.
9.  Shopping cart quantity controls.
10. Pagination or "load more" list using event delegation.

## 21. References

-   [MDN DOM
    Introduction](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction)
-   [MDN
    Document](https://developer.mozilla.org/en-US/docs/Web/API/Document)
-   [MDN Event
    Reference](https://developer.mozilla.org/en-US/docs/Web/Events)
-   [MDN
    EventTarget.addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)
-   [OWASP Cross Site Scripting
    Prevention](https://owasp.org/www-community/xss-filter-evasion-cheatsheet)
