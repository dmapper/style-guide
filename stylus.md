# Stylus Coding Guidelines

Influenced by: 

1. [SUIT CSS](https://github.com/suitcss/suit/blob/master/doc/naming-conventions.md) — meaningful camelCase, hyphens, utility classes.
2. [SUCKS](https://github.com/Team-Sass/SUCKS/blob/sam/thoughts.md) — modifier's naming convention (adjacent class starting with hyphen). 
3. [North](http://pointnorth.io/#css-naming-conventions) — layout elements (starting with underscore)


**Table of contents**

* [JavaScript](#javascript)
  * [JS-targetName, T-targetName](#js-targetName)
* [Utilities](#utilities)
  * [U-utilityName](#u-utilityName)
* [Components](#components)
  * [ComponentName](#componentName)
  * [ComponentName.-modifierName](#componentName.-modifierName)
  * [ComponentName.-stateOfComponent](#componentName.-stateOfComponent)
  * [ComponentName-elementName](#componentName-elementName)
* [Variables](#variables)
  * [Local (component) variable: _property](#property)
  * [Global variables: $ComponentName-property](#componentName-property)
* [Typography](#typography)
  * [Vertical Rhythm](#vertical-rhythm)
  * [font-size and line-height](#fontsize)
* [Formatting](#formatting)
  * [Symbols](#symbols)
  * [Nesting](#nesting)
  * [Spacing](#spacing)
  * [Quotes](#quotes)
* [Performance](#performance)
  * [Specificity](#specificity)


<a name="javascript"></a>
## JavaScript

<a name="js-targetName"></a>
### JS-targetName, T-targetName

Syntax: `JS-<targetName>, T-targetName`

JavaScript-specific classes reduce the risk that changing the structure or theme of components will inadvertently affect any required JavaScript behaviour and complex functionality. It is not neccesarry to use them in every case, just think of them as a tool in your utility belt. If you are creating a class, which you dont intend to use for styling, but instead only as a selector in JavaScript, you should add the `JS-` prefix. The same concept is used to name classes to be used only in the End-to-End tests: `T-` prefix. In practice this looks like this:

```jade
a.Button.-primary.JS-login(href='/login')
```

```jade
a.Button.T-submit(href='/submit')
```

**Again, JavaScript-specific and Test-specific classes should not, under any circumstances, be styled.**

<a name="utilities"></a>
## Utilities

Utility classes are low-level structural and positional traits. Utilities can be applied directly to any element; multiple utilities can be used together; and utilities can be used alongside component classes.

Utilities exist because certain CSS properties and patterns are used frequently. For example: floats, containing floats, vertical alignment, text truncation. Relying on utilities can help to reduce repetition and provide consistent implementations. They also act as a philosophical alternative to functional (i.e. non-polyfill) mixins.


```jade
.u-clearfix
  p.u-textTruncate {{text}}
  img.u-pullLeft(src='{{src}}' alt='')
  img.u-pullLeft(src='{{src}}' alt='')
  img.u-pullLeft(src='{{src}}' alt='')
```

<a name="u-utilityName"></a>
### U-utilityName

Syntax: `U-<utilityName>`

Utilities must use a camel case name, prefixed with a `U` namespace. What follows is an example of how various utilities can be used to create a simple structure within a component.

```jade
.U-clearfix
  a.U-pullLeft(href='{{url}}')
    img.U-block(src='{{src}}' alt='')
  p.U-sizeFill.U-textBreak
    // ... 
```

<a name="components"></a>
## Components

Syntax: `<ComponentName>[-elementName|.-modifierName]`

Component driven development offers several benefits when reading and writing HTML and CSS:

* It helps to distinguish between the classes for the root of the component, descendant elements, and modifications.
* It keeps the specificity of selectors low.
* It helps to decouple presentation semantics from document semantics.

You can think of components as custom elements that enclose specific semantics, styling, and behaviour.


<a name="componentName"></a>
### ComponentName

The component's name must be written in class case.

```sass
.MyComponent 
  // …
```

```jade
article.MyComponent
  // …
```

<a name="ComponentName.-modifierName"></a>
### ComponentName.-modifierName

A component modifier is a class that modifies the presentation of the base component in some form. Modifier names must be written in camel case and start with the hyphen. **Never style these classes directly; they should always be used as an adjoining class.**

The same modifier names can be used in multiple contexts, but every component must define its own styles for the modifier (as they are scoped to the component and used as an adjoining class).

```sass
// Core button
.Btn
  // …
  
  // Default button style
  &.-default
    // …

  // Error button style
  &.-error
    // …
```

```jade
button.Btn.-primary
  // …
```

<a name="componentName.-stateOfComponent"></a>
### ComponentName.-stateOfComponent

States of components are the same as modifiers. Use `-stateName` for state-based modifications of components (i.e. `-hidden`, `-visible`, `-show`, `-expanded`). **Never style these classes directly; they should always be used as an adjoining class.**

JS can add/remove these classes. Other than that there is practically no difference between modifiers and states, so actually when you talk to someone about some state just call it **modifier**.

```sass
.Tweet 
  // …
  &.-expanded
    // …
```

```jade
article.Tweet.-expanded
  // …
```

<a name="componentName-elementName"></a>
### ComponentName-elementName

A component **element** is a class that is attached to a descendant node of a component. It's responsible for applying presentation directly to the descendant on behalf of a particular component. Element names must be written in camel case.

```sass
.Tweet
  background #eee
  &-header
    font-size 30px
  &-avatar
    max-width 100%
  &-body
    background white
```

```jade
article.Tweet
  header.Tweet-header
    img.Tweet-avatar(src='{{src}}' alt='{{alt}}')
    // …
  .Tweet-body
    // …
```

<a name="variables"></a>
## Variables

<a name="property"></a>
### Local (component) variables: _property

Syntax: `_<property>`

Local (compontent) variables (located inside a `.styl` file of a particular JS component) must start with `_` and should NOT be prefixed with the component's name.

The following variable defintion is a color property within HighlightMenu component for usage ONLY in this component.

```sass
// HighlightMenu.styl

_color = rgb(51, 51, 50)

.HighlightMenu
  li
    color _color
```

<a name="componentName-property"></a>
### Global variables: $ComponentName-property

Syntax: `$[ComponentName-]<property>`

If (and only if) you need to use `_first component_`'s variable in a `_second component_` you should make `_first component_`'s local variable into a global file `variables.styl` and change it's name to follow the global variables naming convention.

Global variables MUST be prefixed with the `ComponentName` and start with `$`.

Let's say we want to use the local variable `_color` of `HighlightMenu` from our previous example, in another component `Topbar`. We need to do the following refactoring:

1. Move the constant out of `HighlightMenu.styl` into global `variables.styl` using the global variables naming convention:

```sass
// variables.styl

$HighlightMenu-color = rgb(51, 51, 50)
```

2. Use the global variable to define local variables inside `HighlightMenu` AND `Topbar`. **IMPORTANT! Global variables MUST NOT be used directly to specify CSS properties. Inside a component you MUST first define all global variables as local before using them.** Note that the local variable names can be different in different components, based on their meaning. In `HighlightMenu` we call it just `_color`, while in `Topbar` it's called `_linkColor` to better define the meaning of that color in the topbar.

```sass
// HighlightMenu.styl

_color = $HighlightMenu-color

.HighlightMenu
  li 
    color _color
```

```sass
// Topbar.styl

_color = black
_linkColor = $HighlightMenu-color

.Topbar
  background _color
  a
    color _linkColor
```

<a name="typography"></a>
## Typography


<a name="vertical-rhythm"></a>
### Vertical Rhythm

Basis for our typography is Vertical Rhythm -- page is vertically divided on lines `5px` or `6px` high. 
When you deal with vertical sizes of anything (especially top/bottom margin/padding) try to avoid using `px`, instead use `rym()` function that returns proper size correspending to the specified amount of lines: 

```sass
.Header
  padding-top rym(1)
  padding-bottom @padding-top
  margin-bottom rym(4)
```


<a name="fontsize"></a>
### font-size and line-height

Set font-size either in `px` or `em`. 
Value in `px` will return a corresponding `rem` value and set proper line-height which is going to be equal to the amount of vertical rhythm lines that this font-size can fit into.
Avoid direct usage of line-height.

```sass
p
  font-size 18px
```

If vertical rhythm line is 6px and html font size is 16px it will compile into

```css
p {
  font-size: 1.125rem;
  /* nearest amount of Vertical Rhythm lines is 4 -- 24px */
  line-height: 1.5rem;  
}
```


<a name="formatting"></a>
## Formatting

The following are some high level page formatting style rules.


<a name="symbols"></a>
### Symbols

Don't use any optional in Stylus symbols -- `:`, `;`, `{}`

**Right:**
```css
.Tweet
  color green
  background linear-gradient(#000, #fff)
  a
    color blue
```

**Wrong:**
```css
.Tweet {
  color: green;
  background: linear-gradient(#000, #fff);
  a {
    color: blue;
  }
}
```


<a name="nesting"></a>
### Nesting

Prefer nesting selectors. But no more then 3rd level (pseudo-classes and pseudo-elements doesn't count).

**Right:**
```sass
.Tweet
  &-header
    // …
  &-avatar
    // …
  &-body
    // …
  &-footer
    ul
      display inline-block
    li
      float left
      
.Tweet-footer.-active .Social
  a
    text-decoration none
  &-facebook
    a
      color blue
    span
      font-weight bold
```

**Wrong:**
```sass
.Tweet
  &-footer
    &.-active
      .Social
        // 4th level of nesting. It starts to smell...
        a
          text-decoration none
        &-facebook
          a
            color blue
          span
            font-weight bold
```


<a name="spacing"></a>
### Spacing

Meaningful chunks of code should be seperated by a single new line. It's usually a good idea to separate elements and modifiers even when they have only couple of properties.

**Right:**
```sass
.Content

  background-color black
  color white
  font-size 20px

  &-edit
    color #ccc
    border-radius 10px
    &:hover
      color green
    
  &.-active
    box-shadow 0 0 0 1px green inset
    
  &.-hidden
    display none
```

**Wrong:**
```sass
.Content
  background-color black
  color white
  font-size 20px
  &-edit
    color #ccc
    border-radius 10px
  &.-active
    box-shadow 0 0 0 1px green inset
  &.-hidden
    display none
```


<a name="quotes"></a>
### Quotes

Use single quotes.

**Right:**
```sass
background-image url('/img/you.jpg')
font-family 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial
```

**Wrong:**
```sass
background-image url(/img/you.jpg)
font-family Helvetica Neue Light, Helvetica Neue, Helvetica, Arial
```


<a name="performance"></a>
## Performance

<a name="specificity"></a>
### Specificity

Although in the name (cascading style sheets) cascading can introduce unnecessary performance overhead for applying styles. Take the following example:

```sass
ul.UserList li span a:hover 
  color red
```

Styles are resolved during the renderer's layout pass. The selectors are resolved right to left, exiting when it has been detected the selector does not match. Therefore, in this example every `a` tag has to be inspected to see if it resides inside a `span` and a `li`. As you can imagine this requires a lot of DOM walking and for large documents can cause a significant increase in the layout time.

If we know we want to give all `a` elements inside the `.userList` red on hover we can simplify this style to:

```sass
.UserList a:hover
  color red
```

If we want to only style specific `a` nodes inside `.userList` we can make them **elements** by giving a specific class:

```css
.UserList
  &-linkPrimary:hover
    color red
```
