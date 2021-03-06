# Watch and compile scss dir into css dir
$ sass --watch scss:css

# Variables
Declare variables using the `$`
The convention is to use dashes to separate words.
e.g. `$color-primary: #278da4;`

Define the variable at the top of the scss file, and then reference it whenever it's needed in the file.

What's the scope of a scss variable?

# Nested Selectors
Declare a new rule within an existing rule, to scope the new rule to the existing rule.

e.g. 
``` 
.main-nav {
    margin-top: 1em;
    // scope the li rule to main-nav
    li {
        display: inline-block;
        margin: 0 0.65em;
    }
}
```

You can also use sibling, child, and parent selectors. 
` > li {` to target more specifically.

Best practice says avoid nesting more than two levels deep. It's possible to go turtles all the way down, but it usually means there's a better way forward. 

Use `&` for pseudoclasses.
e.g. 
```
a {
    color: $color-primary;
    text-decoration: none;
    &:hover {
        color: $color-secondary;
    }
}
```

Also use `&` for variations on a class name. 
e.g. `btn-callout` and `btn-info` 
```
.btn {
  color: $white;
  display: inline-block;
  font-weight: bold;
  text-decoration: none;
  text-transform: uppercase;
  padding: 0.75em 1.5em;
  border-radius: 0.35em;
  &-callout {
    font-size: 1.1em;
    background-color: $color-secondary;
  }
  &-info {
  font-size: 0.85em;
    background-color: $color-primary;
    margin-top: auto;
  }
}
```

Also use `&` for reverse nesting, by placing it after the selector. 

e.g. 
```
p {
    margin-bottom: 1.25em;
    .intro & {
        font-size: 1.2em;
        // creates an .intro p {} rule
    }
}
```


# Mixins
A reusable chunk of CSS rules that can be included in other sections. 

Define them. 
e.g
```
@mixin skewed {
    content: '',
    display: block;
    width: 100%;
    height: 50px;
    position: absolute;
    transform: skewY(-2deg);
}
```

Include them. 
e.g.
```
footer {
    padding: 2em;
    background-color: $color-shade;
    @include skewed;
}
```

You must define a mixin before it's included. 

# Mixins + Nested Selectors + Pseudoelements
You can pass blocks of styles including pseudoelements. 
```
@mixin skewed {
    &::after {
        content: '',
        display: block;
        width: 100%;
        height: 50px;
        position: absolute;
        transform: skewY(-2deg);    
    }
}
```

You can also pass in custom content. 
```
@mixin skewed {
    position: relative;
    &::after {
        content: '',
        display: block;
        width: 100%;
        height: 50px;
        position: absolute;
        transform: skewY(-2deg);
        @content;    
    }
}
```

Define the content in the mixin. Then pass it in during the import by passing the content into the curly braces.
e.g. 
```
header {
    background-color: $color-primary;
    p {
        color: $color-accent;
        font-size: 1.25em;
        margin: 0;
    }
    @include skewed {
        // content goes here...
        background-color: $color-shade;
        bottom: -25px;
    }
}
```

# Extend the properties of selectors
Use `@extend` to take an existing set of rules for a selector, and build upon them. 

e.g. 
```
.btn {
    color: $white;
    display: inline-block;
    font-weight: bold;
    text-decoration: none;
    text-transform: uppercase;
    padding: 0.75em 1.5em;
    border-radius: 0.35em;
}

.btn-callout {
    @extend .btn;
    font-size: 1.1em;
    background-color: $color-secondary;
}
```


Use placeholder selector to mark something as a rule block that will be extended, but which won't be output when not extended. 

e.g. `%btn {}` vs. `.btn {}`

We can further streamline the code by using nested selectors on top of placeholders and extensions. 

e.g. 
```
%btn {
    color: $white;
    display: inline-block;
    font-weight: bold;
    text-decoration: none;
    text-transform: uppercase;
    padding: 0.75em 1.5em;
    border-radius: 0.35em;
}

.btn {
    @extend %btn;
    &-callout {
        font-size: 1.1em;
        background-color: $color-secondary;
    }
    &-info {
        font-size 0.85em;
        background-color: $color-primary;
    } 
}
```

Placeholders are also useful for utility classes that you want to extend, but don't want to export. 

e.g. 
```
%clearfix {
    &::after {
        content: '';
        display: table;
        clear: both;
    }
}
```

Then use `@extend clearfix` in any rule set where I want to clear floats (instead of cluttering up my HTML).

Placeholders share nested rules with other selectors. 
e.g. 
```
%btn {
    color: $white;
    display: inline-block;
    font-weight: bold;
    text-decoration: none;
    text-transform: uppercase;
    padding: 0.75em 1.5em;
    border-radius: 0.35em;
    &:hover {
        color: $white;
        opacity: 0.8;
    }
    &:active {
        opacity: initial;
    }
}
```

Using opacity and extending is a great way to handle hover and active states for all links and buttons. 

It's a best practice to not over-extend. Don't simulataneously extend multiple selectors. 
e.g. 
```
// DON'T DO THIS
.btn, .icn, .card, .table {
    @extends .btn;
}
```

# Comments
Single line comments in scss use `//`.
Single line comments are not exported into css. 

Multi-line comments are wrapped in `/*` and `*/` (just like CSS). 
Multi-line comments are exported to css. 
