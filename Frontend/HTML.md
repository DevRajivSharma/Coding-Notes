# HTML --- Complete Study Notes

> Practical notes covering HTML fundamentals, semantic structure, forms,
> accessibility, SEO, and production best practices.

## Table of Contents

-   [1. What Is HTML?](#1-what-is-html)
-   [2. Basic HTML Document](#2-basic-html-document)
-   [3. Elements, Tags, and Attributes](#3-elements-tags-and-attributes)
-   [4. Headings, Paragraphs, and Text](#4-headings-paragraphs-and-text)
-   [5. Links and Navigation](#5-links-and-navigation)
-   [6. Images and Media](#6-images-and-media)
-   [7. Lists](#7-lists)
-   [8. Tables](#8-tables)
-   [9. Forms and Inputs](#9-forms-and-inputs)
-   [10. Semantic HTML](#10-semantic-html)
-   [11. Block vs Inline Elements](#11-block-vs-inline-elements)
-   [12. Global Attributes](#12-global-attributes)
-   [13. HTML Entities and Comments](#13-html-entities-and-comments)
-   [14. Accessibility](#14-accessibility)
-   [15. SEO and Metadata](#15-seo-and-metadata)
-   [16. Script and Stylesheet
    Loading](#16-script-and-stylesheet-loading)
-   [17. Forms: Validation and
    Security](#17-forms-validation-and-security)
-   [18. Common Mistakes](#18-common-mistakes)
-   [19. Interview Questions](#19-interview-questions)
-   [20. Practice Tasks](#20-practice-tasks)
-   [21. References](#21-references)

## 1. What Is HTML?

**HTML (HyperText Markup Language)** describes the structure and meaning
of content on a web page. It is a markup language, not a programming
language.

-   HTML defines structure and meaning.
-   CSS controls presentation and layout.
-   JavaScript adds behavior and interactivity.

Browsers parse HTML into a **DOM (Document Object Model)** tree that CSS
and JavaScript can work with.

## 2. Basic HTML Document

``` html
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>My First Page</title>
  <meta name="description" content="A short description of this page.">
</head>
<body>
  <header>
    <h1>Hello, web!</h1>
  </header>

  <main>
    <p>This is my first HTML page.</p>
  </main>
</body>
</html>
```

-   `<!doctype html>` selects modern HTML parsing mode.
-   `<html lang="en">` is the document root and declares the language.
-   `<head>` contains metadata and resource references.
-   `<meta charset="UTF-8">` specifies character encoding.
-   The viewport meta tag helps pages render at the device width.
-   `<title>` supplies the browser-tab title and often the search-result
    title.
-   `<body>` contains visible page content.

## 3. Elements, Tags, and Attributes

An element commonly consists of an opening tag, content, and a closing
tag:

``` html
<p class="intro">Welcome!</p>
```

-   Tag: `p`
-   Attribute: `class="intro"`
-   Content: `Welcome!`

Some elements are void elements and do not have closing tags, such as
`<img>`, `<input>`, `<br>`, `<hr>`, and `<meta>`.

### Attributes

``` html
<a href="/about" target="_blank" rel="noopener">About us</a>
```

Attributes configure elements. Quote attribute values consistently.
Boolean attributes are enabled by presence, for example
`<input required>`.

### Nesting

Correct nesting:

``` html
<p>This is <strong>important</strong>.</p>
```

Avoid overlapping tags or placing elements in invalid parents. Valid
nesting helps browsers, assistive technologies, and scripts interpret
the page reliably.

## 4. Headings, Paragraphs, and Text

``` html
<h1>Page title</h1>
<h2>Main section</h2>
<h3>Subsection</h3>
<p>A paragraph of text.</p>
<strong>Important content</strong>
<em>Emphasized content</em>
<mark>Highlighted content</mark>
<small>Small print</small>
<blockquote cite="https://example.com">A longer quotation.</blockquote>
<code>const total = 3;</code>
<pre><code>Line one
Line two</code></pre>
```

Use headings to represent document hierarchy, not merely to change font
size. Use CSS for visual styling. `<strong>` indicates importance; `<b>`
draws attention without adding the same semantic importance. `<em>`
conveys emphasis; `<i>` is used for text in an alternate voice or mood,
such as a technical term.

## 5. Links and Navigation

``` html
<a href="https://developer.mozilla.org/">MDN Web Docs</a>
<a href="/contact">Contact</a>
<a href="#pricing">Jump to pricing</a>
<a href="mailto:hello@example.com">Email us</a>
<a href="tel:+911234567890">Call us</a>
```

A link uses `href`. Use meaningful link text rather than vague text like
"click here."

For links opened in a new tab:

``` html
<a href="https://example.com" target="_blank" rel="noopener noreferrer">
  Open external site
</a>
```

Modern browsers generally imply `noopener` for `_blank`, but writing it
explicitly communicates intent. `noreferrer` also suppresses the
referrer header.

Use `<a>` for navigation and `<button>` for actions.

## 6. Images and Media

``` html
<img
  src="/images/team.jpg"
  alt="Three teammates working at a desk"
  width="800"
  height="500"
  loading="lazy"
>
```

-   `src` identifies the image.
-   `alt` provides a text alternative. Use `alt=""` for purely
    decorative images.
-   Width and height help browsers reserve layout space.
-   `loading="lazy"` can defer off-screen images; do not lazy-load the
    main above-the-fold hero image by default.

Responsive image:

``` html
<picture>
  <source media="(max-width: 600px)" srcset="/images/team-small.webp">
  <img src="/images/team-large.webp" alt="Team collaborating">
</picture>
```

Video:

``` html
<video controls width="640" poster="/images/video-cover.jpg">
  <source src="/media/demo.mp4" type="video/mp4">
  Your browser does not support embedded video.
</video>
```

Provide captions or transcripts when needed. Avoid autoplaying audio and
respect user preferences.

## 7. Lists

``` html
<ul>
  <li>React</li>
  <li>Node.js</li>
</ul>

<ol>
  <li>Install dependencies</li>
  <li>Start the server</li>
</ol>

<dl>
  <dt>API</dt>
  <dd>An interface for software to communicate.</dd>
</dl>
```

Use unordered lists when sequence is not important, ordered lists for a
sequence, and description lists for term-description pairs.

## 8. Tables

Use tables for tabular data, not page layout.

``` html
<table>
  <caption>Monthly expenses</caption>
  <thead>
    <tr>
      <th scope="col">Category</th>
      <th scope="col">Amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">Transport</th>
      <td>1200</td>
    </tr>
    <tr>
      <th scope="row">Food</th>
      <td>3500</td>
    </tr>
  </tbody>
</table>
```

Use `<caption>` and appropriate header cells to improve accessibility.
Complex tables may need more explicit header associations.

## 9. Forms and Inputs

``` html
<form action="/signup" method="post">
  <label for="full-name">Full name</label>
  <input id="full-name" name="fullName" autocomplete="name" required>

  <label for="email">Email</label>
  <input id="email" name="email" type="email" autocomplete="email" required>

  <label for="password">Password</label>
  <input id="password" name="password" type="password" minlength="8" required>

  <button type="submit">Create account</button>
</form>
```

Important attributes: - `name`: key used when submitting form data. -
`id`: uniquely identifies an element in the document. - `type`:
determines input behavior. - `required`, `min`, `max`, `minlength`,
`maxlength`, and `pattern`: browser-side validation constraints. -
`autocomplete`: helps browsers fill common information. - `placeholder`:
a hint, not a replacement for a label.

Common input types include `text`, `email`, `password`, `number`,
`date`, `checkbox`, `radio`, `file`, `tel`, `url`, and `hidden`.

### Select, textarea, and fieldset

``` html
<label for="role">Role</label>
<select id="role" name="role">
  <option value="student">Student</option>
  <option value="developer">Developer</option>
</select>

<label for="bio">Short bio</label>
<textarea id="bio" name="bio" rows="4"></textarea>

<fieldset>
  <legend>Preferred contact</legend>
  <label><input type="radio" name="contact" value="email"> Email</label>
  <label><input type="radio" name="contact" value="phone"> Phone</label>
</fieldset>
```

Use the same `name` for radio buttons in one group. Checkbox groups may
share a name when the server expects multiple values, depending on form
handling.

## 10. Semantic HTML

Semantic elements communicate purpose.

  Element                       Purpose
  ----------------------------- ------------------------------------------------------
  `<header>`                    Introductory content for a page or section
  `<nav>`                       Major navigation links
  `<main>`                      Main content of the document
  `<section>`                   Thematic grouping, typically with a heading
  `<article>`                   Self-contained composition
  `<aside>`                     Complementary content
  `<footer>`                    Footer for a page or section
  `<figure>` / `<figcaption>`   Illustrative content and caption
  `<details>` / `<summary>`     Disclosure widget
  `<time>`                      Date/time value
  `<dialog>`                    Dialog interface, usually controlled with JavaScript

Example structure:

``` html
<body>
  <header>
    <a href="/">My Portfolio</a>
    <nav aria-label="Main navigation">
      <a href="/projects">Projects</a>
      <a href="/contact">Contact</a>
    </nav>
  </header>

  <main>
    <section>
      <h1>Projects</h1>
      <article>
        <h2>LiveDesk</h2>
        <p>A collaborative workspace project.</p>
      </article>
    </section>
  </main>

  <footer>Copyright information</footer>
</body>
```

Prefer native semantic elements over generic `<div>` and `<span>` when
an element matches the content's meaning. A `<div>` is still appropriate
for grouping without specific semantics.

## 11. Block vs Inline Elements

This distinction is a useful introductory model, but CSS display rules
can change how elements are laid out.

-   Common block elements: `<div>`, `<p>`, `<section>`, `<h1>`.
-   Common inline elements: `<span>`, `<a>`, `<strong>`, `<em>`.

Block elements typically start on a new line in normal flow. Inline
elements typically flow within text. Use CSS `display` to control
layout.

## 12. Global Attributes

Common attributes usable on many HTML elements:

-   `id`: unique identifier within a document.
-   `class`: reusable styling or selection hook.
-   `title`: advisory information; do not rely on it for essential
    instructions.
-   `hidden`: hides an element from rendering.
-   `data-*`: custom data attributes.
-   `tabindex`: controls focusability and tab order; avoid positive
    values in ordinary interfaces.
-   `contenteditable`: enables editing content in the browser.
-   `dir`: text direction, such as `ltr` or `rtl`.
-   `lang`: language of content.
-   `aria-*`: accessibility attributes used when native semantics are
    insufficient.

Example:

``` html
<button class="save-button" data-record-id="42">Save</button>
```

JavaScript can read the custom attribute through
`element.dataset.recordId`.

## 13. HTML Entities and Comments

Entities represent reserved characters or special symbols:

``` html
<p>Use &lt; and &gt; to represent angle brackets.</p>
<p>Copyright &copy; 2026</p>
<p>Tom &amp; Jerry</p>
```

Comments:

``` html
<!-- This is a comment. -->
```

Comments are visible in page source and are not a place for passwords,
tokens, or confidential data.

## 14. Accessibility

Accessibility helps people with disabilities use a website, including
users of screen readers, keyboard navigation, magnification, and voice
control.

Checklist: - Use semantic HTML and a logical heading order. - Provide
meaningful image alternatives. - Associate every form control with a
label. - Ensure keyboard access and visible focus. - Use buttons for
actions and links for navigation. - Provide captions or transcripts for
relevant media. - Do not convey information by color alone. - Use ARIA
only when native HTML semantics do not meet the need. - Test keyboard
operation and, when possible, assistive technologies.

Example:

``` html
<button type="button" aria-expanded="false" aria-controls="menu">
  Toggle menu
</button>
<nav id="menu" hidden>
  <a href="/about">About</a>
</nav>
```

The attributes alone do not implement the toggle. JavaScript must update
`aria-expanded` and the `hidden` state together.

## 15. SEO and Metadata

``` html
<head>
  <title>MERN Developer Portfolio | Rajiv</title>
  <meta name="description" content="Projects and experience in full-stack web development.">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="canonical" href="https://example.com/portfolio">
</head>
```

Practical guidelines: - Give each important page a descriptive title and
useful meta description. - Use meaningful headings and descriptive link
text. - Use appropriate canonical URLs when duplicate URL variants
exist. - Make important content accessible to crawlers. - Use structured
data only when it accurately describes the page. - Metadata can support
search presentation but does not guarantee ranking.

## 16. Script and Stylesheet Loading

``` html
<link rel="stylesheet" href="/css/styles.css">
<script src="/js/app.js" defer></script>
```

-   External stylesheets are usually referenced in `<head>`.
-   `defer` downloads a classic external script while parsing and runs
    it after parsing, preserving order among deferred classic scripts.
-   `async` runs when the script is ready and does not preserve
    execution order.
-   Module scripts (`type="module"`) are deferred by default.
-   Put scripts at the end of `<body>` only when that approach suits the
    page; `defer` is often clearer for classic scripts.

## 17. Forms: Validation and Security

HTML validation improves user experience, but it is not a security
boundary.

-   Validate all important data on the server.
-   Treat submitted data as untrusted.
-   Use CSRF protections where appropriate for cookie-authenticated
    state-changing requests.
-   Escape or safely render untrusted content to prevent XSS.
-   Use HTTPS for sensitive form submissions.
-   Do not expose secrets in hidden inputs; users can inspect and modify
    them.

## 18. Common Mistakes

1.  Using headings only to get a desired font size.
2.  Using a placeholder instead of a label.
3.  Forgetting the `alt` attribute on images.
4.  Using `<div>` for every structural element.
5.  Using links to trigger actions or buttons for navigation.
6.  Omitting `name` from form inputs that must be submitted.
7.  Using duplicate `id` values.
8.  Assuming browser validation replaces server validation.
9.  Loading large images without dimensions or optimization.
10. Placing secrets in HTML or comments.

## 19. Interview Questions

**1. Is HTML a programming language?**\
No. It is a markup language used to structure content.

**2. What is semantic HTML?**\
HTML elements whose names convey the meaning and role of their content.

**3. What is the difference between `id` and `class`?**\
An `id` should uniquely identify one element in a document; a `class`
can be shared by many elements.

**4. What is the difference between `div` and `span`?**\
Both are generic containers. `div` is block-level by default; `span` is
inline by default.

**5. What is the difference between `GET` and `POST` in a form?**\
`GET` places submitted data in the URL query string and is appropriate
for retrieval; `POST` sends data in the request body. Neither
automatically makes data secure---HTTPS and proper server controls still
matter.

**6. What is the DOM?**\
A programming interface representing a document as a tree of nodes that
scripts can inspect and modify.

**7. Why is `alt` important?**\
It provides a text alternative for meaningful images.

**8. What does `defer` do?**\
It allows a classic external script to download during parsing and
execute after HTML parsing is complete.

**9. What is the difference between an element and a tag?**\
A tag is markup such as `<p>`; an element is the complete node,
including its content and tags where applicable.

**10. Why use a label in a form?**\
It communicates a field's purpose and helps users, including those using
assistive technology, focus the control.

## 20. Practice Tasks

1.  Build a personal profile page with headings, paragraphs, and links.
2.  Create a semantic portfolio layout with header, nav, main, sections,
    and footer.
3.  Create a project table with accessible row and column headings.
4.  Build a signup form with labels and native validation.
5.  Add responsive images with useful alternative text.
6.  Create a navigation menu that can be operated by keyboard.
7.  Add metadata and a meaningful title to each page.
8.  Embed a video with controls and provide a transcript or captions
    when appropriate.

## 21. References

-   [MDN HTML Guide](https://developer.mozilla.org/en-US/docs/Web/HTML)
-   [MDN HTML Elements
    Reference](https://developer.mozilla.org/en-US/docs/Web/HTML/Element)
-   [WHATWG HTML Standard](https://html.spec.whatwg.org/)
-   [W3C Web Accessibility Initiative](https://www.w3.org/WAI/)
