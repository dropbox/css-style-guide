# Dropbox (S)CSS Style Guide

> “Every line of code should appear to be written by a single person, no matter the number of contributors.” —@mdo

## General
### Don’ts

- Avoid using HTML tags in CSS selectors
  - E.g. Prefer `.o-modal {}` over `div.o-modal {}`
  - Always prefer using a class over HTML tags (with some exceptions like CSS resets)
- Don't use ids in selectors
  - `#header` is overly specific compared to, for example `.header` and is much harder to override
  - Read more about the headaches associated with IDs in CSS [here](http://csswizardry.com/2011/09/when-using-ids-can-be-a-pain-in-the-class/).
- Don’t nest more than 3 levels deep
  - Nesting selectors increases specificity, meaning that overriding any CSS set therein needs to be targeted with an even more specific selector. This quickly becomes a significant maintenance issue.
- Avoid using nesting for anything other than pseudo selectors and state selectors.
  - E.g. nesting `:hover`, `:focus`, `::before`, etc. is OK, but nesting selectors inside selectors should be avoided.
- Don't `!important`
  - Ever.
  - If you must, leave a comment, and prioritise resolving specificity issues before resorting to `!important`.
  - `!important` greatly increases the power of a CSS rule, making it extremely tough to override in the future. It’s only possible to override with another `!important` rule later in the cascade.
- Don’t use `margin-top`.
  - Vertical margins [collapse](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing). Always prefer `padding-top` or`margin-bottom` on preceding elements
- Avoid shorthand properties (unless you really need them)
  - It can be tempting to use, for instance, `background: #fff` instead of `background-color: #fff`, but doing so overrides other values encapsulated by the shorthand property. (In this case, `background-image` and its associative properties are set to “none.”
  - This applies to all properties with a shorthand: border, margin, padding, font, etc.

### Spacing

- Four spaces for indenting code
- Put spaces after `:` in property declarations
  - E.g. `color: red;` instead of `color:red;`
- Put spaces before `{` in rule declarations
  - E.g. `.o-modal {` instead of `.o-modal{`
- Write your CSS one line per rule
- Add a line break after `}` closing rule declarations
- When grouping selectors, keep individual selectors on a single line
- Place closing braces `}` on a new line
- Add a new line at the end of .scss files
- Trim excess whitespace

### Formatting

- All selectors are lower case, hyphen separated aka “spinal case” eg. `.my-class-name`
- Always prefer Sass’s double-slash `//` commenting, even for block comments
- Avoid specifying units for zero values, e.g. `margin: 0;` instead of `margin: 0px;`
- Always add a semicolon to the end of a property/value rule
- Use leading zeros for decimal values `opacity: 0.4;` instead of `opacity: .4;`
- Put spaces before and after child selector `div > span` instead of `div>span`

----------

## Sass Specifics
### Internal order of a .scss file

1. Imports
2. Variables
3. Base Styles
4. Experiment Styles

Example:

```scss
//------------------------------
// Modal
//------------------------------

@import "../constants";
@import "../helpers";

$DBmodal-namespace: "c-modal" !default;
$DBmodal-padding: 32px;

$DBmodal-background: #fff !default;
$DBmodal-background-alt: color(gray, x-light) !default;

.o-modal { ... }

// Many lines later...

// EXPERIMENT: experiment-rule-name
.o-modal--experiment { ... }
// END EXPERIMENT: experiment-rule-name
```

### Variables

- Define all variables at the top of the file after the imports
- Namespace local variables with the filename (SASS has no doc level scope)
  - eg `business_contact.scss` →`$business_contact_font_size: 14px;`
- Local variables should be `$snake_lowercase`
- Global constants should be `$SNAKE_ALL_CAPS`

### Color

- Use the defined color constants via the color function
- Lowercase all hex values `#fffff`
- Limit alpha values to a maximum of two decimal places. Always use a leading zero.

Example:

```scss
// Bad
.c-link {
  color: #007ee5;
  border-color: #FFF;
  background-color: rgba(#FFF, .0625);
}

// Good
.c-link {
  color: color(blue);
  border-color: #ffffff;
  background-color: rgba(#ffffff, 0.1);
}
```

### Experiments

Wrap experiment styles with comments:

```scss
// EXPERIMENT: experiment-rule-name
.stuff { ... }
// END EXPERIMENT: experiment-rule-name
```

----------

## Rule Ordering

Properties and nested declarations should appear in the following order, with line breaks between groups:

1. Any `@` rules
2. Layout and box-model properties
  - margin, padding, box-sizing, overflow, position, display, width/height, etc.
3. Typographic properties
  - E.g. font-*, line-height, letter-spacing, text-*, etc.
4. Stylistic properties
  - color, background-*, animation, border, etc.
5. UI properties
  - appearance, cursor, user-select, pointer-events, etc.
6. Pseudo-elements
  - ::after, ::before, ::selection, etc.
7. Pseudo-selectors
  - :hover, :focus, :active, etc.
8. Modifier classes
9. Nested elements

Here’s a comprehensive example:

```scss
.c-btn {
    @extend %link--plain;

    display: inline-block;
    padding: 6px 12px;

    text-align: center;
    font-weight: 600;

    background-color: color(blue);
    border-radius: 3px;
    color: white;

    &::before {
        content: '';
    }

    &:focus, &:hover {
        box-shadow: 0 0 0 1px color(blue, .3);
    }

    &--big {
        padding: 12px 24px;
    }

    > .c-icon {
        margin-right: 6px;
    }
}
```

----------

## Nesting

- As a general rule of thumb, avoid nesting selectors more than 3 levels deep
- Prefer using nesting as a convenience to extend the parent selector over targeting nested elements. For example:
  ```scss
  .block {
      padding: 24px;

      &--mini {
          padding: 12px;
      }
  }
  ```

Nesting can be really easily avoided by smart class naming (with the help of BEM) and avoiding bare tag selectors.

----------

## BEM

Block: Unique, meaningful names for a logical unit of style. Avoid excessive shorthand.
- Good: `.alert-box` or `.recents-intro` or `.button`
- Bad: `.feature` or `.content` or `.btn`

Element: styles that only apply to children of a block. Elements can also be blocks themselves. Class name is a concatenation of the block name, two underscores and the element name. Examples:
- `.alert-box__close`
- `.expanding-section__section`

Modifier: override or extend the base styles of a block or element with modifier styles. Class name is a concatenation of the block (or element) name, two hyphens and the modifier name. Examples:
- `.alert-box--success`
- `.expanding-section--expanded`

### BEM Best practices

Don't `@extend` block modifiers with the block base.
- Good: `<div class="my-block my-block--modifier">`
- Bad: `<div class="my-block--modifier">`

Don't create elements inside elements. If you find yourself needing this, consider converting your element into a block.
- Bad: `.alert-box__close__button`

Choose your modifiers wisely. These two rules have very different meaning:

```scss
.block--modifier .block__element { color: red; }
.block__element--modifier { color: red; }
```

----------

## Selector Naming

- Try to use [BEM-based](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/) naming for your class selectors
  - When using modifier classes, always require the base/unmodified class is present
- Use Sass’s nesting to manage BEM selectors like so:
  ```scss
  .block {
      &--modifier { // compiles to .block--modifier
          text-align: center;
      }

      &__element { // compiles to .block__element
          color: red;

          &--modifier { // compiles to .block__element--modifier
              color: blue;
          }
      }
  }
  ```

----------

## Namespaced Classes

There are a few reserved namespaces for classes to provide common and globally-available abstractions.

- `.o-` for CSS objects. Objects are usually common design patterns (like the Flag object). Modifying these classes could have severe knock-on effects.
- `.c-` for CSS components. Components are designed pieces of UI—think buttons, inputs, modals, and banners.
- `.u-` for helpers and utilities. Utility classes are usually single-purpose and have high priority. Things like floating elements, trimming margins, etc.
- `.is-, .has-` for stateful classes, a la [SMACSS](https://smacss.com/book/type-state). Use these classes for temporary, optional, or short-lived states and styles.
- `._` for hacks. Classes with a hack namespace should be used when you need to force a style with `!important` or increasing specificity, should be temporary, and should not be bound onto.
- `.t-` for theme classes. Pages with unique styles or overrides for any objects or components should make use of theme classes.

----------

## Separation of Concerns (One Thing Well™)

You should always try to spot common code—padding, font sizes, layout patterns—and abstract them to reusable, namespaced classes that can be chained to elements and have a single responsibility. Doing so helps prevent overrides and duplicated rules, and encourages a separation of concerns.

```scss
// Bad code
// HTML:
// <div class="modal compact">...</div>
.modal {
    padding: 32px;
    background-color: color(gray, x-light);

    &.compact {
        padding: 24px;
    }
}

// Good code
// HTML:
// <div class="c-modal u-l-island">...</div>
// <div class="c-modal u-l-isle">...</div>

// components/_modal.scss
.c-modal {
    background-color: color(gray, x-light);
}

// helpers/_layout.scss
.u-l-island {
    padding: 32px;
}

.u-l-isle {
    padding: 24px;
}
```

----------

## Media Queries

Media queries should be within the CSS selector as per SMACSS

```scss
.selector {
      float: left;

      @media only screen and (max-width: 767px) {
        float: none;
      }
}
```

Create variables for frequently used breakpoints

```scss
$SCREEN_SM_MAX: "max-width: 767px";

.selector {
      float: left;

      @media only screen and ($SCREEN_SM_MAX) {
        float: none;
      }
}
```
