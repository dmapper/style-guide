# Stylus Coding Guidelines

Influenced by: 

1. [SUIT CSS](https://github.com/suitcss/suit/blob/master/doc/naming-conventions.md) — meaningful camelCase, hyphens, utility classes.
2. [SUCKS](https://github.com/Team-Sass/SUCKS/blob/sam/thoughts.md) — modifier's naming convention (adjacent class starting with hyphen). 
3. [North](http://pointnorth.io/#css-naming-conventions) — layout elements (starting with underscore)


**Table of contents**

* [JavaScript](#javascript)
  * [js-targetName](#js-targetName)
* [Utilities](#utilities)
  * [u-utilityName](#u-utilityName)
* [Components](#components)
  * [componentName](#componentName)
  * [componentName.-modifierName](#componentName.-modifierName)
  * [componentName.-stateOfComponent](#componentName.-stateOfComponent)
  * [componentName-elementName](#componentName-elementName)
* [Layouts](#layouts)
  * [_layoutComponentName](#_layoutComponentName)
* [Variables](#variables)
  * [$componentName-property-value](#componentName-property-value)
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
### js-targetName

Syntax: `js-<targetName>`

JavaScript-specific classes reduce the risk that changing the structure or theme of components will inadvertently affect any required JavaScript behaviour and complex functionality. It is not neccesarry to use them in every case, just think of them as a tool in your utility belt. If you are creating a class, which you dont intend to use for styling, but instead only as a selector in JavaScript, you should probably be adding the `js-` prefix. In practice this looks like this:

```jade
a.btn.-primary.js-login(href='/login')
```

**Again, JavaScript-specific classes should not, under any circumstances, be styled.**

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
### u-utilityName

Syntax: `u-<utilityName>`

Utilities must use a camel case name, prefixed with a `u` namespace. What follows is an example of how various utilities can be used to create a simple structure within a component.

```jade
.u-clearfix
  a.u-pullLeft(href='{{url}}')
    img.u-block(src='{{src}}' alt='')
  p.u-sizeFill.u-textBreak
    // ... 
```

<a name="components"></a>
## Components

Syntax: `<componentName>[-elementName|.-modifierName]`

Component driven development offers several benefits when reading and writing HTML and CSS:

* It helps to distinguish between the classes for the root of the component, descendant elements, and modifications.
* It keeps the specificity of selectors low.
* It helps to decouple presentation semantics from document semantics.

You can think of components as custom elements that enclose specific semantics, styling, and behaviour.


<a name="componentName"></a>
### componentName

The component's name must be written in camel case.

```sass
.myComponent 
  // …
```

```jade
article.myComponent
  // …
```

<a name="componentName.-modifierName"></a>
### componentName.-modifierName

A component modifier is a class that modifies the presentation of the base component in some form. Modifier names must be written in camel case and start with the hyphen. **Never style these classes directly; they should always be used as an adjoining class.**

The same modifier names can be used in multiple contexts, but every component must define its own styles for the state (as they are scoped to the component and used as an adjoining class).

```sass
// Core button
.btn
  // …
  
  // Default button style
  &.-default
    // …

  // Error button style
  &.-error
    // …
```

```jade
button.btn.-primary
  // …
```

<a name="componentName.-stateOfComponent"></a>
### componentName.-stateOfComponent

States of components are the same as modifiers. Use `-stateName` for state-based modifications of components (i.e. `-hidden`, `-visible`, `-show`, `-expanded`). **Never style these classes directly; they should always be used as an adjoining class.**

JS can add/remove these classes. Other than that there is practically no difference between modifiers and states, so actually when you talk to someone about some state just call it **modifier**.

```sass
.tweet 
  // …
  &.-expanded
    // …
```

```jade
article.tweet.-expanded
  // …
```

<a name="componentName-elementName"></a>
### componentName-elementName

A component **element** is a class that is attached to a descendant node of a component. It's responsible for applying presentation directly to the descendant on behalf of a particular component. Element names must be written in camel case.

```sass
.tweet
  background #eee
  &-header
    font-size 30px
  &-avatar
    max-width 100%
  &-body
    background white
```

```jade
article.tweet
  header.tweet-header
    img.tweet-avatar(src='{{src}}' alt='{{alt}}')
    // …
  .tweet-body
    // …
```

<a name="layouts"></a>
## Layouts

<a name="_layoutComponentName"></a>
### _layoutComponentName

Syntax: `<_layoutComponentName>[-layoutElementName|.-modifierName]`

Layouts are the structure of an interface. Providing structure to pages and components, layouts are responsible for sizing and positioning of their elements. Layouts are generally used for laying out pages and regions within pages. Layouts that include viewport based media queries (width, height, etc…) should never be nested inside each other.

Layouts start with an underscore (`_`) and written in camelCase. They can have elements and modifiers and everything else like regular components.

Layouts can be page-specific. In this case nest it under `body` with specific class name of current view.

```sass
// General layout
._page
  background-color #eee
  max-width 800px
  margin 0 auto

// Layout for 'users' controller, 'show' action
body.-users-show
  ._page
    display flex
    max-width 1200px
  ._left
    width 20%
    &-section
      background red
  ._content
    width 80%
    &-section
      background blue
```

```jade
._page
  ._left
    ._left-section
    ._left-section
  ._content
    ._content-section
    ._content-section
```


<a name="variables"></a>
## Variables

<a name="componentName-property-value"></a>
### $componentName-property-value

Syntax: `$[componentName-]<property>-<value>`

Variable names in our CSS are also strictly structured. This syntax provides strong associations between property, use, and component.

Global and component-level variables MUST start with `$`.  
Local variables inside functions and mixins may omit leading `$`.

The following variable defintion is a color property, with the value grayLight, for use with the HighlightMenu component.

```sass
// highlightMenu.styl

$highlightMenu-color-greyLight ?= rgb(51, 51, 50)

.highlightMenu
  li 
    color: $highlightMenu-color-greyLight
```

All component variables should be defined inside the component file and using existential operator `?=`.

If you need to use _first component_'s variable in a _second component_ you should duplicate this variable into global `variables.styl` but using `=` operator this time:

```sass
// variables.styl
$topbar-height = 80px

// topbar.styl
$topbar-height ?= 80px
.topbar
  height $topbar-height

// topmenu.styl
.topmenu
  line-height $topbar-height
```


<a name="typography"></a>
## Typography


<a name="vertical-rhythm"></a>
### Vertical Rhythm

Basis for our typography is Vertical Rhythm -- page is vertically divided on lines `5px` or `6px` high. 
When you deal with vertical sizes of anything (especially top/bottom margin/padding) try to avoid using `px`, instead use `rym()` function that returns proper size correspending to the specified amount of lines: 

```sass
.header
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
.tweet
  color green
  background linear-gradient(#000, #fff)
  a
    color blue
```

**Wrong:**
```css
.tweet {
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
.tweet
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
      
.tweet-footer.-active .social
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
.tweet
  &-footer
    &.-active
      .social
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
.content

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
.content
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
ul.userList li span a:hover 
  color red
```

Styles are resolved during the renderer's layout pass. The selectors are resolved right to left, exiting when it has been detected the selector does not match. Therefore, in this example every `a` tag has to be inspected to see if it resides inside a `span` and a `li`. As you can imagine this requires a lot of DOM walking and for large documents can cause a significant increase in the layout time.

If we know we want to give all `a` elements inside the `.userList` red on hover we can simplify this style to:

```sass
.userList a:hover
  color red
```

If we want to only style specific `a` nodes inside `.userList` we can make them **elements** by giving a specific class:

```css
.userList
  &-linkPrimary:hover
    color red
```
