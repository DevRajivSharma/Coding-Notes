# CSS --- Complete Study Notes

> Practical reference for styling web pages, responsive layouts,
> animations, maintainable architecture, and CSS interview preparation.

## Table of Contents

-   [1. What Is CSS?](#1-what-is-css)
-   [2. Ways to Add CSS](#2-ways-to-add-css)
-   [3. Selectors](#3-selectors)
-   [4. Cascade, Specificity, and
    Inheritance](#4-cascade-specificity-and-inheritance)
-   [5. Colors and Units](#5-colors-and-units)
-   [6. Box Model](#6-box-model)
-   [7. Display and Visibility](#7-display-and-visibility)
-   [8. Positioning and Stacking](#8-positioning-and-stacking)
-   [9. Flexbox](#9-flexbox)
-   [10. CSS Grid](#10-css-grid)
-   [11. Typography](#11-typography)
-   [12. Backgrounds and Borders](#12-backgrounds-and-borders)
-   [13. Pseudo-classes and
    Pseudo-elements](#13-pseudo-classes-and-pseudo-elements)
-   [14. Responsive Design](#14-responsive-design)
-   [15. Transitions and Animations](#15-transitions-and-animations)
-   [16. CSS Variables](#16-css-variables)
-   [17. Overflow and Scroll](#17-overflow-and-scroll)
-   [18. Modern CSS Functions](#18-modern-css-functions)
-   [19. Architecture and
    Maintainability](#19-architecture-and-maintainability)
-   [20. Performance and
    Accessibility](#20-performance-and-accessibility)
-   [21. Common Mistakes](#21-common-mistakes)
-   [22. Interview Questions](#22-interview-questions)
-   [23. Practice Tasks](#23-practice-tasks)
-   [24. References](#24-references)

## 1. What Is CSS?

**CSS (Cascading Style Sheets)** describes how HTML elements are
presented: colors, typography, spacing, layout, responsive behavior, and
animation.

A CSS rule contains a selector and declarations:

``` css
.card {
  padding: 1rem;
  border-radius: 0.75rem;
  background-color: white;
}
```

-   `.card` is the selector.
-   `padding`, `border-radius`, and `background-color` are properties.
-   Values follow each property.

## 2. Ways to Add CSS

### External stylesheet (recommended for most projects)

``` html
<link rel="stylesheet" href="/css/styles.css">
```

### Internal stylesheet

``` html
<style>
  body { font-family: system-ui, sans-serif; }
</style>
```

### Inline style

``` html
<p style="color: crimson;">Notice</p>
```

External stylesheets improve reuse and organization. Inline styles are
useful in some generated or dynamic situations but are harder to
maintain and override.

## 3. Selectors

``` css
/* Element */
p { line-height: 1.6; }

/* Class */
.card { padding: 1rem; }

/* ID */
#main-header { position: sticky; }

/* Group */
h1, h2, h3 { line-height: 1.2; }

/* Descendant */
.card p { color: #444; }

/* Direct child */
.menu > li { list-style: none; }

/* Attribute */
input[type="email"] { width: 100%; }

/* Multiple conditions */
button.primary { font-weight: 700; }

/* Adjacent sibling */
h2 + p { margin-top: 0; }

/* General sibling */
h2 ~ p { color: #333; }
```

### Common combinators

-   Space: descendant.
-   `>`: direct child.
-   `+`: immediately following sibling.
-   `~`: later sibling with the same parent.

Prefer class selectors for reusable component styling. Avoid overly long
selectors that tightly couple CSS to HTML structure.

## 4. Cascade, Specificity, and Inheritance

When multiple declarations compete, the browser considers origin and
importance, cascade layers, specificity, scoping proximity where
applicable, and source order. The exact cascade is more nuanced than
"the last rule always wins."

A simplified specificity comparison:

-   Inline style: high author-level precedence, but not above every
    possible important declaration.
-   ID selector: `#menu`
-   Class, attribute, pseudo-class: `.active`, `[disabled]`, `:hover`
-   Type and pseudo-element: `button`, `::before`

Example:

``` css
p { color: black; }
.note { color: blue; }
#warning { color: red; }
```

An element with `id="warning"` and `class="note"` matches all three
rules; absent other cascade factors, the ID selector wins.

### Inheritance

Text-related properties such as `color` and `font-family` commonly
inherit. Box properties such as `margin` and `border` generally do not.

``` css
body {
  color: #222;
  font-family: system-ui, sans-serif;
}
```

### `!important`

``` css
.notice { color: red !important; }
```

It changes cascade priority and makes later overrides harder. Use
sparingly; prefer clear component rules and controlled cascade layers.

## 5. Colors and Units

Color formats:

``` css
.box {
  color: #222;
  background: rgb(245 245 245);
  border-color: hsl(210 20% 80%);
}
```

Useful units:

-   `px`: CSS pixel.
-   `%`: relative to a context defined by the property.
-   `rem`: relative to the root element's font size.
-   `em`: relative to the relevant element's font size, with
    property-specific behavior.
-   `vw`, `vh`: viewport width and height units.
-   `dvh`, `svh`, `lvh`: dynamic, small, and large viewport-height units
    where supported.
-   `fr`: fraction of available space in CSS Grid.
-   `ch`: approximate width of the `0` glyph in the current font.

Use relative units where they support responsive layouts and user text
scaling. Avoid assuming `100vh` always matches the visible mobile
viewport; modern viewport units may be more suitable.

## 6. Box Model

Every element's box is affected by content, padding, border, and margin.

``` css
*,
*::before,
*::after {
  box-sizing: border-box;
}
```

With `content-box`, declared width normally applies to content only.
With `border-box`, width includes content, padding, and border, making
layout sizing easier.

``` css
.card {
  width: 300px;
  padding: 20px;
  border: 2px solid #ddd;
  margin: 16px;
}
```

Margins are outside the border; padding is inside it. Vertical margins
may collapse in certain normal-flow layouts.

## 7. Display and Visibility

-   `block`: starts on a new line in normal flow and typically takes
    available width.
-   `inline`: flows within text; width and height generally do not
    behave like block boxes.
-   `inline-block`: flows inline but accepts box dimensions.
-   `none`: removes the element from layout.
-   `flex`: creates a flex formatting context.
-   `grid`: creates a grid formatting context.

Visibility differences:

``` css
.hidden { display: none; }
.invisible { visibility: hidden; }
```

`display: none` removes layout space. `visibility: hidden` normally
preserves the space. For accessibility-sensitive hiding, choose a
deliberate technique rather than assuming these are interchangeable.

## 8. Positioning and Stacking

``` css
.static { position: static; }
.relative { position: relative; }
.absolute { position: absolute; }
.fixed { position: fixed; }
.sticky { position: sticky; top: 0; }
```

-   `static`: normal positioning.
-   `relative`: remains in normal flow but can be offset; often
    establishes a containing block for absolute descendants.
-   `absolute`: removed from normal flow and positioned relative to a
    containing block.
-   `fixed`: generally positioned relative to the viewport, though
    certain ancestors can change the containing block.
-   `sticky`: behaves like relative until a threshold, then sticks
    within its scrolling context.

### `z-index`

Controls stacking order in relevant stacking contexts. A large `z-index`
does not guarantee an element will appear above every other element
because stacking contexts are isolated.

Common stacking-context triggers include positioned elements with a
non-auto `z-index`, transforms, opacity below 1, and several other
properties.

## 9. Flexbox

Flexbox is primarily a one-dimensional layout system: row or column.

``` css
.container {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  gap: 1rem;
  flex-wrap: wrap;
}
```

Main properties:

-   `flex-direction`: `row`, `row-reverse`, `column`, `column-reverse`.
-   `justify-content`: alignment along the main axis.
-   `align-items`: alignment along the cross axis.
-   `align-self`: alignment for an individual item.
-   `flex-wrap`: whether items wrap.
-   `gap`: space between items.
-   `flex-grow`, `flex-shrink`, `flex-basis`: determine how items
    distribute space.

Center an item:

``` css
.center {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 200px;
}
```

Remember that the main axis changes when `flex-direction` changes.

## 10. CSS Grid

Grid is ideal for two-dimensional layouts involving rows and columns.

``` css
.product-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 1rem;
}
```

Responsive auto-fit grid:

``` css
.product-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(min(100%, 220px), 1fr));
  gap: 1rem;
}
```

Useful concepts:

-   `grid-template-columns` and `grid-template-rows`
-   `gap`
-   `grid-column` and `grid-row`
-   `place-items` and `place-content`
-   `minmax()`
-   `repeat()`
-   `auto-fit` and `auto-fill`
-   named grid areas using `grid-template-areas`

Use Flexbox for one-dimensional alignment and Grid when you need
coordinated rows and columns. They can be combined.

## 11. Typography

``` css
body {
  font-family: system-ui, -apple-system, "Segoe UI", sans-serif;
  font-size: 1rem;
  line-height: 1.6;
  color: #202124;
}

h1 {
  font-size: clamp(2rem, 5vw, 3.5rem);
  line-height: 1.1;
  letter-spacing: -0.03em;
}
```

Important properties include `font-family`, `font-size`, `font-weight`,
`line-height`, `letter-spacing`, `text-align`, `text-decoration`,
`text-transform`, and `overflow-wrap`.

Use readable line lengths, adequate line height, and sufficient
contrast. Avoid fixed text sizes that prevent comfortable zooming or
user preferences.

## 12. Backgrounds and Borders

``` css
.hero {
  background-color: #f8e9ed;
  background-image: linear-gradient(120deg, #f8e9ed, #e4edff);
  border: 1px solid #ddd;
  border-radius: 1rem;
  box-shadow: 0 8px 24px rgb(0 0 0 / 10%);
}
```

Useful properties: `background-size`, `background-position`,
`background-repeat`, `border-radius`, `outline`, and `box-shadow`.

Use `outline` or other visible focus styling for keyboard navigation. Do
not remove focus indicators without a clear replacement.

## 13. Pseudo-classes and Pseudo-elements

Pseudo-classes select an element in a state:

``` css
button:hover { filter: brightness(0.95); }
button:focus-visible { outline: 3px solid royalblue; }
input:invalid { border-color: crimson; }
li:first-child { font-weight: 700; }
tr:nth-child(even) { background: #f7f7f7; }
```

Pseudo-elements style a part of an element or generate decorative
content:

``` css
.required-label::after {
  content: " *";
  color: crimson;
}

blockquote::before {
  content: "“";
  font-size: 2em;
}
```

Do not use generated content as the only source of essential
information. Test interaction states beyond hover, including keyboard
focus and disabled states.

## 14. Responsive Design

Responsive design adapts to screen sizes and user settings.

``` html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

Mobile-first approach:

``` css
.layout {
  display: grid;
  grid-template-columns: 1fr;
  gap: 1rem;
}

@media (min-width: 768px) {
  .layout {
    grid-template-columns: 250px minmax(0, 1fr);
  }
}
```

Use breakpoints based on where the content needs them rather than
targeting a list of specific devices. Other tools include flexible
widths, `max-width`, `min()`, `max()`, `clamp()`, responsive images, and
container queries.

``` css
.page {
  width: min(100% - 2rem, 72rem);
  margin-inline: auto;
}
```

## 15. Transitions and Animations

Transition:

``` css
.button {
  transition: background-color 160ms ease, transform 160ms ease;
}

.button:hover {
  transform: translateY(-2px);
}
```

Keyframe animation:

``` css
@keyframes fade-in {
  from { opacity: 0; transform: translateY(8px); }
  to { opacity: 1; transform: translateY(0); }
}

.panel {
  animation: fade-in 250ms ease-out both;
}
```

Respect reduced-motion preferences:

``` css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

For smooth performance, prefer animating `transform` and `opacity` when
possible. Avoid unnecessary continuous animations.

## 16. CSS Variables

``` css
:root {
  --color-primary: #e85d75;
  --color-text: #202124;
  --radius-card: 0.75rem;
  --space-4: 1rem;
}

.button {
  background: var(--color-primary);
  border-radius: var(--radius-card);
  padding: var(--space-4);
}
```

Custom properties support shared design tokens, themes, and runtime
updates.

``` css
[data-theme="dark"] {
  --color-text: #f5f5f5;
}
```

A variable must be defined or have a fallback:

``` css
.card { color: var(--text-color, #222); }
```

## 17. Overflow and Scroll

``` css
.card {
  overflow: hidden;
}

.code-block {
  overflow-x: auto;
}

.scroll-panel {
  overflow-y: auto;
  max-height: 20rem;
}
```

Common values: `visible`, `hidden`, `clip`, `scroll`, `auto`. `hidden`
can still allow programmatic scrolling in some cases; `clip` is designed
to clip without creating a scroll container. Choose intentionally.

For long words and grid/flex children, `min-width: 0` or
`overflow-wrap: anywhere` may prevent unwanted overflow.

## 18. Modern CSS Functions

``` css
.container { width: min(100% - 2rem, 70rem); }
.title { font-size: clamp(2rem, 4vw, 4rem); }
.grid { grid-template-columns: repeat(3, minmax(0, 1fr)); }
.panel { padding: calc(1rem + 1vw); }
```

-   `calc()`: calculates values.
-   `min()` / `max()`: choose the smaller/larger value.
-   `clamp(min, preferred, max)`: constrain a fluid value.
-   `minmax()`: defines a range for Grid tracks.
-   `aspect-ratio`: establishes a preferred width-to-height ratio.
-   Container queries adapt a component to its container rather than the
    viewport.

Container query example:

``` css
.card-list { container-type: inline-size; }

@container (min-width: 600px) {
  .card { display: grid; grid-template-columns: 1fr 2fr; }
}
```

## 19. Architecture and Maintainability

Practical guidelines: - Use reusable component classes and design
tokens. - Keep selectors understandable and avoid unnecessary
specificity. - Group styles by component or feature. - Use a naming
convention such as BEM if it helps the team. - Remove unused styles
carefully. - Use cascade layers when a project benefits from explicit
precedence.

Example BEM naming: `.card`, `.card__title`, `.card--featured`.

CSS Modules, utility-first systems such as Tailwind, and
component-scoped styles are alternatives; choose based on project needs
and team conventions.

## 20. Performance and Accessibility

-   Avoid large, unused stylesheets.
-   Minimize layout shifts by reserving image space.
-   Prefer simple selectors and avoid unnecessary DOM complexity.
-   Test responsive layouts at intermediate widths, not only phone and
    desktop presets.
-   Maintain sufficient text/background contrast.
-   Preserve keyboard focus indicators.
-   Do not rely on color alone to convey status.
-   Support reduced-motion preferences.
-   Test zoom and text resizing.
-   Use browser DevTools to inspect computed styles, layout, and
    stacking contexts.

## 21. Common Mistakes

1.  Confusing `justify-content` and `align-items`.
2.  Forgetting that Flexbox axes depend on `flex-direction`.
3.  Using `position: absolute` for an entire page layout.
4.  Adding huge `z-index` values without understanding stacking
    contexts.
5.  Overusing `!important`.
6.  Forgetting `box-sizing: border-box`.
7.  Using fixed widths that cause horizontal scrolling.
8.  Removing focus outlines.
9.  Animating layout-heavy properties unnecessarily.
10. Assuming CSS rules always win based only on source order.

## 22. Interview Questions

**1. What is CSS specificity?**\
A system used by the cascade to compare selector weight when
declarations compete at the relevant cascade priority.

**2. What is the box model?**\
The content, padding, border, and margin areas associated with an
element's box.

**3. Flexbox vs Grid?**\
Flexbox is primarily one-dimensional; Grid is designed for
two-dimensional rows and columns. They complement each other.

**4. `display: none` vs `visibility: hidden`?**\
The former removes the element from layout; the latter usually preserves
its layout space.

**5. Relative vs absolute positioning?**\
Relative positioning keeps the element in normal flow; absolute
positioning removes it from normal flow and positions it relative to its
containing block.

**6. What does `z-index` do?**\
It influences stacking order within the relevant stacking context.

**7. What is responsive design?**\
Designing layouts that adapt to different viewports, input methods, and
user settings.

**8. What are pseudo-classes and pseudo-elements?**\
Pseudo-classes select states or structural conditions; pseudo-elements
target a part of an element or generated content.

**9. What is inheritance?**\
Some properties pass computed values from parent to descendants, such as
`font-family` and often `color`.

**10. What is the difference between `px`, `em`, and `rem`?**\
`px` is a CSS pixel; `em` is relative to the relevant font size; `rem`
is relative to the root element's font size.

**11. Why use CSS variables?**\
To share design values, make themes consistent, and simplify updates.

**12. How do you debug unexpected CSS?**\
Inspect computed styles, matched rules, specificity, cascade layers,
inherited values, layout boxes, and stacking contexts in DevTools.

## 23. Practice Tasks

1.  Create a responsive navigation bar.
2.  Build a card grid with Grid and `auto-fit`.
3.  Center a modal using Flexbox or Grid.
4.  Make a responsive two-column dashboard.
5.  Create a reusable color and spacing token system.
6.  Add hover, focus, active, and disabled states to a button.
7.  Implement dark mode with CSS custom properties.
8.  Respect reduced-motion settings.
9.  Fix a layout with horizontal overflow.
10. Recreate a portfolio section using mobile-first CSS.

## 24. References

-   [MDN CSS Guide](https://developer.mozilla.org/en-US/docs/Web/CSS)
-   [MDN CSS
    Reference](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference)
-   [CSS Flexible Box
    Layout](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_flexible_box_layout)
-   [CSS Grid
    Layout](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout)
-   [web.dev Learn CSS](https://web.dev/learn/css/)
