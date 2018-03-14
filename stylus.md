# Stylus Coding Guidelines

Influenced by: 

1. [SUIT CSS](https://github.com/suitcss/suit/blob/master/doc/naming-conventions.md) — meaningful camelCase, hyphens, utility classes.
2. [SUCKS](https://github.com/Team-Sass/SUCKS/blob/sam/thoughts.md) — modifier's naming convention (adjacent class starting with hyphen). 
3. [North](http://pointnorth.io/#css-naming-conventions) — layout elements (starting with underscore)


**Table of contents**

* [Components](#components)
  * [ComponentName](#componentName)
  * [ComponentName-elementName](#componentName-elementName)  
  * [ComponentName.-modifierName](#componentName.-modifierName)
* [Variables](#variables)
  * [Local (component) variable: _property](#property)
  * [Global variables: $ComponentName-property](#componentName-property)
* [JavaScript](#javascript)
  * [JS-targetName, T-targetName](#js-targetName)
* [Utilities](#utilities)
  * [U-utilityName](#u-utilityName)
* [Formatting](#formatting)
  * [Symbols](#symbols)
  * [Nesting](#nesting)
  * [Spacing](#spacing)
  * [Quotes](#quotes)

<a name="components"></a>
## Components

Syntax: `ComponentName[-elementName][.-modifierName]`

Component driven development offers several benefits when reading and writing HTML and CSS:

* It helps to distinguish between the classes for the root of the component, descendant elements, and modifications.
* It keeps the specificity of selectors low.
* It helps to decouple presentation semantics from document semantics.

You can think of components as custom elements that enclose specific semantics, styling, and behaviour.


<a name="componentName"></a>
### ComponentName

The component's name must be written in class case.

```styl
.MyComponent 
  color red
```

```html
<div className='MyComponent' />
```

<a name="componentName-elementName"></a>
### ComponentName-elementName

A component **element** is a class that is attached to a descendant node of a component. It's responsible for applying presentation directly to the descendant on behalf of a particular component. Element names must be written in camel case.

```styl
.Tweet
  background #eee
  &-header
    font-size 30px
  &-avatar
    max-width 100%
  &-body
    background white
```

```html
<div className='Tweet'>
  <div className='Tweet-header'>
    <img className='Tweet-avatar' src={src} alt={alt} />
  </div>
  <div className='Tweet-body' />
</div> 
```

<a name="componentName.-modifierName"></a>
### ComponentName.-modifierName

Syntax: 
1. `ComponentName.-modifierName`
2. `ComponentName-elementName.-modifierName`

A modifier is a class that modifies the presentation of the base component or element in some way. Modifier names must be written in camel case and start with the hyphen. **Never style these classes directly; they should always be used as an adjoining class.**

You should also use modifiers to define states of the component: `-hidden`, `-visible`, `-show`, `-expanded`, etc.

The same modifier names can be used in multiple contexts, but every component must define its own styles for the modifier (as they are scoped to the component/element and used as an adjoining class).

```styl
.Btn
  color black              // Default button style

  &.-error
    color red              // Error button style
    
.Topbar
  background-color blue    // Default topbar style
  
  &-link    
    color blue             // Default link style
    &.-active              
      color white          // When link is active
      
  &.-dark                  
    background-color black // Dark topbar style
    
```

```html
<button className='Btn -primary'>Submit</button>
<div className='Topbar -dark'>
  <a className='Topbar-link -active' href='/'>Home</a>
  <a className='Topbar-link' href='/about'>About</a>
  <a className='Topbar-link' href='/contact'>Contact us</a>
</div>
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

Syntax: `$ComponentName-<property>`

If (and only if) you need to use `_first component_`'s variable in a `_second component_` you should make `_first component_`'s local variable into a global file `variables.styl` and change it's name to follow the global variables naming convention.

Global variables MUST be prefixed with the `ComponentName` and start with `$`.

Let's say we want to use the local variable `_color` of `HighlightMenu` from our previous example, in another component `Topbar`. We need to do the following refactoring:

1. Move the constant out of `HighlightMenu.styl` into global `variables.styl` using the global variables naming convention:

```styl
// variables.styl

$HighlightMenu-color = rgb(51, 51, 50)
```

2. Use the global variable to define local variables inside `HighlightMenu` AND `Topbar`. **IMPORTANT! Global variables MUST NOT be used directly to specify CSS properties. Inside a component you MUST first define all global variables as local before using them.** Note that the local variable names can be different in different components, based on their meaning. In `HighlightMenu` we call it just `_color`, while in `Topbar` it's called `_linkColor` to better define the meaning of that color in the topbar.

```styl
// HighlightMenu.styl

_color = $HighlightMenu-color

.HighlightMenu
  li 
    color _color
```

```styl
// Topbar.styl

_color = black
_linkColor = $HighlightMenu-color

.Topbar
  background _color
  a
    color _linkColor
```

<a name="javascript"></a>
## JavaScript

<a name="js-targetName"></a>
### JS-targetName, T-targetName

Syntax: `JS-<targetName>, T-targetName`

JavaScript-specific classes reduce the risk that changing the structure or theme of components will inadvertently affect any required JavaScript behaviour and complex functionality. It is not neccesarry to use them in every case, just think of them as a tool in your utility belt. If you are creating a class, which you dont intend to use for styling, but instead only as a selector in JavaScript, you should add the `JS-` prefix. The same concept is used to name classes to be used only in the End-to-End tests: `T-` prefix. In practice this looks like this:

```html
<a className='Button -primary JS-login' href='/login' />
```

```html
<a className='Button T-submit' href='/submit' />
```

**Again, JavaScript-specific and Test-specific classes should not, under any circumstances, be styled.**

<a name="utilities"></a>
## Utilities

Utility classes are low-level structural and positional traits. Utilities can be applied directly to any element; multiple utilities can be used together; and utilities can be used alongside component classes.

Utilities exist because certain CSS properties and patterns are used frequently. For example: floats, containing floats, vertical alignment, text truncation. Relying on utilities can help to reduce repetition and provide consistent implementations. They also act as a philosophical alternative to functional (i.e. non-polyfill) mixins.


```html
<div className='U-clearfix'>
  <p className='U-textTruncate'>{text}</p>
  <img className='U-pullLeft' src={src} alt='' />
</div> 
```

<a name="u-utilityName"></a>
### U-utilityName

Syntax: `U-<utilityName>`

Utilities must use a camel case name, prefixed with a `U` namespace. What follows is an example of how various utilities can be used to create a simple structure within a component.

```html
<div className='U-clearfix'>
  <a className='U-pullLeft' href={url}>
    <img className='U-block' src={src} alt='' />
  </a>  
  <p className='U-sizeFill U-textBreak'>Lorem Ipsum</p>
</div> 
```


<a name="formatting"></a>
## Formatting

The following are some high level page formatting style rules.


<a name="symbols"></a>
### Symbols

Don't use any optional in Stylus symbols -- `:`, `;`, `{}`

**Right:**
```styl
.Tweet
  color green
  background linear-gradient(#000, #fff)
  a
    color blue
```

**Wrong:**
```styl
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
```styl
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
```styl
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
```styl
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
```styl
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
```styl
background-image url('/img/you.jpg')
font-family 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial
```

**Wrong:**
```styl
background-image url(/img/you.jpg)
font-family Helvetica Neue Light, Helvetica Neue, Helvetica, Arial
```
