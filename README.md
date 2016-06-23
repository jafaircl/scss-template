# SCSS Starter Template
## ITCSS
"ITCSS is a sane, scalable, managed architecture for CSS"
[More info in this presentation by Harry Roberts](http://csswizardry.net/talks/2014/11/itcss-dafed.pdf)

####SCSS broken down based on specificity:
Settings: These are globally available settings. This is where we define SASS variables, lists and maps. For example: brand colors, default spacing units, breakpoint maps and typography maps.
```scss
// Brand colors
$primary-color: #FF69B4;

// Default spacing units
$spacing-unit: 10px;

// Breakpoint map
$breakpoints: (
  default: null,
  s: '(min-width: 480px)',
  m: '(min-width: 840px)',
  l: '(min-width: 1280px)'
);

// Font size map that will correspond to breakpoints
$font-sizes: (
  default: 16px,
  s: 18px,
  m: 19px,
  l: 20px
);
```
Tools: Globally available tools such as public mixins and helper functions.
```scss
@function calculateRem($size) {
  $remSize: $size / 16px;
  @return $remSize * 1rem;
}

@mixin font-size($size) {
  font-size: $size;
  font-size: calculateRem($size);
}
```
Generic: Ground-zero, universal styles. CSS resets, Normalize.css, box-sizing, etc. live here.
```scss
html {
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}

* {
  &,
  &:before,
  &:after {
    -webkit-box-sizing: inherit;
       -moz-box-sizing: inherit;
            box-sizing: inherit;
    }

}
```
Elements: This is where HTML elements are styled.
```scss
a {
  color: inherit;
  text-decoration: none;
  text-shadow: 1px 1px 0 $background-color, 
              -1px 1px 0 $background-color, 
               2px 0 0 $background-color, 
              -2px 0 0 $background-color;
  box-shadow: inset 0 -2px 0 0 $link-color;
  
  &:hover {
    color: $link-color;
    box-shadow: inset 0 -2px 0 0 $link-color,
                0 2px 0 0 $link-color;
  }
}
```
Objects: Design patterns such as a grid framework, UI lists, etc. This is the first layer with classes.
```scss
@each $key, $breakpoint in $breakpoints {
  @if $breakpoint == null {
    @for $i from 1 through $total-columns {
      @if map-has-key($grid-map, $i) {
        .o-grid__column--#{$i}\/#{$total-columns} {
          width: map-get($grid-map, $i);
        }
      }
    }
  }
  @else {
    @media #{$breakpoint} {
      @for $i from 1 through $total-columns {
        @if map-has-key($grid-map, $i) {
          .o-grid__column--#{$i}\/#{$total-columns}\@#{$key} {
            width: map-get($grid-map, $i);
          }
        }
      }
    }
  }
}
```
Components: Designed pieces of the UI that are more explicitly named.
```scss
[class*='c-card__rich-media'] {
  position: relative;
  width: 100%;
  overflow: hidden;
  
  &:before {
    content: "";
    display: block; 
  }
  
  & > * {
    position: absolute;
    min-width: 100%; min-height: 100%;
    top: 50%; left: 50%;
    transform: translate(-50%,-50%);
  }
}

.c-card__rich-media--16\/9:before {
  padding-top: 56.25%;
}

.c-card__rich-media--4\/3:before {
  padding-top: 75%;
}

.c-card__rich-media--1\/1:before {
  padding-top: 100%;
}
```
Trumps: ⋅Overrides, helpers and utilities. These are only for one piece of the DOM at a time.
```scss
.clearfix,
%clearfix {

    &:after {
        content: "";
        display: table;
        clear: both;
    }

}
```

## BEMIT
BEM naming convention with namespaces
[More info here.](http://csswizardry.com/2015/08/bemit-taking-the-bem-naming-convention-a-step-further/)
[And here.](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)
BEM is the "Block, Element Modifier" naming convention. An example is:
```css
.person {}
.person__hand {}
.person--female {}
.person--female__hand {}
.person__hand--left {}
```
The point is to allow someone looking at the HTML code to immediately tell what each pice of markup is doing based just on the class name. BEMIT takes it a step further by adding namespaces. [From Harry Roberts:](http://csswizardry.com/2015/03/more-transparent-ui-code-with-namespaces/)
* o-: Signify that something is an Object, and that it may be used in any number of unrelated contexts to the one you can currently see it in. Making modifications to these types of class could potentially have knock-on effects in a lot of other unrelated places. Tread carefully.
* c-: Signify that something is a Component. This is a concrete, implementation-specific piece of UI. All of the changes you make to its styles should be detectable in the context you’re currently looking at. Modifying these styles should be safe and have no side effects.
* u-: Signify that this class is a Utility class. It has a very specific role (often providing only one declaration) and should not be bound onto or changed. It can be reused and is not tied to any specific piece of UI. You will probably recognise this namespace from libraries and methodologies like SUIT.
* t-: Signify that a class is responsible for adding a Theme to a view. It lets us know that UI Components’ current cosmetic appearance may be due to the presence of a theme.
* s-: Signify that a class creates a new styling context or Scope. Similar to a Theme, but not necessarily cosmetic, these should be used sparingly—they can be open to abuse and lead to poor CSS if not used wisely.
* is-, has-: Signify that the piece of UI in question is currently styled a certain way because of a state or condition. This stateful namespace is gorgeous, and comes from SMACSS. It tells us that the DOM currently has a temporary, optional, or short-lived style applied to it due to a certain state being invoked.
* _: Signify that this class is the worst of the worst—a hack! Sometimes, although incredibly rarely, we need to add a class in our markup in order to force something to work. If we do this, we need to let others know that this class is less than ideal, and hopefully temporary (i.e. do not bind onto this).
* js-: Signify that this piece of the DOM has some behaviour acting upon it, and that JavaScript binds onto it to provide that behaviour. If you’re not a developer working with JavaScript, leave these well alone.
* qa-: Signify that a QA or Test Engineering team is running an automated UI test which needs to find or bind onto these parts of the DOM. Like the JavaScript namespace, this basically just reserves hooks in the DOM for non-CSS purposes.