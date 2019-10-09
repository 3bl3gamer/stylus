---
layout: default
permalink: docs/lists.html
---

# Lists

Stylus list is a collection of different kind of items. In another word, it is an array, map, list in other programming languages. 

## Syntax
It syntax is similar to CSS syntax for shorthand properties. But it is not completely alike.

### Basic - One-dimensional list
Lists are created like variables, each item is separated by a space `' '` or commas `,`. The comma could be use in multi-line list or multidimensional list.

**Stylus**

    /**
    * Defining syntax
    * [1] variable-name = item-1 item-2 item-etc
    * [2] variable-name = item-1, item-2, item-etc
    */
    border-items-list = 1px solid #000

    /**
    * Getter syntax
    * (Item's position of list always start at zero)
    * variable-name[item-position]
    */
    div {
        border-width: border-items-list[0]
        border-style: border-items-list[1]
        border-color: border-items-list[2]
    }
    
    // Or like this
    div.no2 {
        border: border-items-list
    }
    

**CSS**

    div {
        border-width: 1px
        border-style: solid
        border-color: #000
    }
    
    // Or like this
    div.no2 {
        border: 1px solid #000
    }

**Help yourself**: [test it online][basic-ls]

### Above basic - Multidimensional list
Multidimensional list, aka. nest list, is made by `()` or adding various list variables to another variable. Its separator is the same as a single dimension list.

**Stylus**: parentheses style

    /**
    * Multidimensional list: () style
    * morther = (list-00 list-01 list-02) item-1 (list-20 list-21 list-22)
    */
    mother-list = (1px solid #000) (2px solid #321) none (3px solid #341)

**Stylus**: parentheses style

    /**
    * Multidimensional list: () style
    * morther = list-00 list-01 list-02, item-1, list-20 list-21 list-22
    */
    mother-list = 1px solid #000, 2px solid #321, none, 3px solid #341


Be aware that `()` operator and `,` doesn't create a list, it works as a level-separator.

Which means doing this `list = ((x))` doesn't create a multidimensional list with single item, in fact, the `()` will be ignore and the list will be a plain variable `list = x`. Learn more at [Separator ()](#separator-).

On the other hand `,` will cause a bug if it is used like this `wrong = x,`.


**Stylus**: nested variables style

When an empty variable included an list variable, it will be turned into list variable.  
When a value variable included an list variable, it will be turned into a multidimensional list.

    /**
    * Multidimensional list: nested variables style
    * morther = child-list-0 child-1 child-list-etc
    * child-list-0 = 0 1 2 etc
    * child-1 = 'child-variable'
    * child-list-etc = 3 2 1
    */
    mother-list = child-list-0 child-list-1 child-2 child-list-3
    child-list-0 = 1px solid #000
    child-list-1 = 2px solid #321
    child-2 = none
    child-list-3 = 3px solid #341
    
Because of this it can no add another item to list by including it. To add new item, use its [BIF](#bif).
    
**Stylus**: usage

    div {
        border: mother-list[1]
    }
    div.deeper {
        border-color: mother-list[3][2]
    }

**CSS**

    /**
    * No matter what style is used, only one result in return.
    */
    
    div {
        border: 2px solid #321
    }
    div.deeper {
        border-color: #341
    }

**Help yourself**: [test it online][nested-ls]
    

### Almost advance - Map
Map is a key-value list. It's allow to access to list with a string address. In Stylus, map can be created by hash object. Because of this, this technique is also called hash list.



### Advance - Multi-line list
One-line list is useful for a small piece of code. However, stylus lists're usually used as a data holder so the code is quite large. Keeping it in one-line could upset colleagues. As result, this section was made for the sense of justice, the clean code morality.

#### Multi-line list
In order to cut down the size of one-line to multi-line, separator comma `,` is used. The first line must have simple syntax with the comma at the end of line `variable = item,`. Following line can be indent or not. The end of list could use semi-colon `;` for readable reason, this is optional.

The comma, in this case, can be understand as an indicator which tells that parser that the next line is an item of the closet defined variable `=`. With this in mind, we can see that the line has no comma is the end of list.

**Stylus**

    /**
    * multi-line = first-item,  -> Beginning must have the comma (,)
    *   2nd-item,
    *   3, 4, 5,
    *   (6 7 8),
    *   the-end                 -> the end must not have the comma (,)
    */
    list = (5px 5px blue),
        (10px 10px red),
        (15px 15px green)
        
    div.shadow {
        box-shadow: list[0]
    }
    
    div.superShadow {
        box-shadow: list
    }
        
**CSS**

    div.shadow {
        box-shadow: 5px 5px blue
    }
    
    div.superShadow {
        box-shadow: 5px 5px blue,
                    10px 10px red,
                    15px 15px green;
    }

**Help yourself**: [test it online][multiline-ls]
    
It is strongly recommended to have multi-line for nested list. It also quite convenience for some properties has multi-value.

#### Multi-line map
Comma is useful in simple case, but if the list is wrap in a hash object/map, the comma become a problem. Because of that, a trick was found to fool the parser. In short, we will make the parser thought it still in a line, but from users' view it's actually more than a line.

The parser detect an item of a list by separator (which are comma and space). But what inside the item is a different story. The secret lies in the pattern -> close Parentheses or Curly Braces | space/single item | open Parentheses or Curly Braces <-. Something like this `} (` or `){`.

**Stylus**

    /**
    * multi-line = {        <- must have
    *       first-hash
    *   } {
    *       2nd-hash
    *   } (
    *       3, 4, 5
    *   ) (
    *       6 7 8
    *   ) {
    *       the-end
    *   }
    */
    map = {
        border: {
            thin: 1px,
            thick: 999px
        } solid {
            light: #FFF,
            dark: #000,
            peach: pink
        },
        shadow: (
            5px 5px blue
        )(
            10px 10px red
        )(
            15px 15px green
        )
    }
    
    div.border {
        border: map.border[0].thin map.border[1] map.border[2].peach
    }
    
    div.shadow {
        box-shadow: unquote(join(',', map.shadow))
    }

**Help yourself**: [test it online][multiline-map]


## In-action

### Operators

#### Separator ()

#### Subscript []

#### Range .. …

#### Additive: + -

### Iteration

## BIF
BIF stands for Build-in function. Below is a list of BIF for handling list.

- [Push(list, args…)](docs/bifs.html#pushexpr-args) | add
- [unshift(list, args…)](docs/bifs.html#unshiftexpr-args) | add
- [pop(list)](docs/bifs.html#popexpr) | remove
- [shift(list)](docs/bifs.html#shiftexpr) | remove
- [index(list, value)](docs/bifs.html#indexlist-value) | info
- [list-separator(list)](docs/bifs.html#list-separatorlist) | info
- [range(start, stop[, step])](docs/bifs.html#rangestart-stop-step) | generate
- [join(delim, lists/vals…)](docs/bifs.html#joindelim-vals) | convert list -> str
- [split(delim, val)](docs/bifs.html#splitdelim-val) | convert str/ident -> list



[basic-ls]: http://stylus-lang.com/try.html#?code=border-items-list%20%3D%201px%20solid%20%23000%0A%0Adiv%20%7B%0A%20%20%20%20border-width%3A%20border-items-list%5B0%5D%0A%20%20%20%20border-style%3A%20border-items-list%5B1%5D%0A%20%20%20%20border-color%3A%20border-items-list%5B2%5D%0A%7D%0A%0Adiv.no2%20%7B%0A%20%20%20%20border%3A%20border-items-list%0A%7D

[nested-ls]: http://stylus-lang.com/try.html#?code=border%20%3D%20(1px%20solid%20%23000)%20(2px%20solid%20%23321)%20(3px%20solid%20%23341)%20none%0A%0Aborder-alter%20%3D%201px%20solid%20%23000%2C%202px%20solid%20%23321%2C%203px%20solid%20%23341%2C%20none%0A%0Ashadow%20%3D%20list-0%2C%20list-1%2C%20list-2%0Alist-0%20%3D%205px%205px%20blue%0Alist-1%20%3D%2010px%2010px%20red%0Alist-2%20%3D%2015px%2015px%20green%0A%0Amotherbox%20%7B%0A%20%20border%3A%20border%5B0%5D%0A%20%20box-shadow%3A%20shadow%0A%7D

[multiline-ls]: http://stylus-lang.com/try.html#?code=list%20%3D%20(5px%205px%20blue)%2C%0A%20%20%20%20(10px%2010px%20red)%2C%0A%20%20%20%20(15px%2015px%20green)%0A%20%20%20%20%0Adiv.shadow%20%7B%0A%20%20%20%20box-shadow%3A%20list%5B0%5D%0A%7D%0A%0Adiv.superShadow%20%7B%0A%20%20%20%20box-shadow%3A%20list%0A%7D

[multiline-map]: http://stylus-lang.com/try.html#?code=map%20%3D%20%7B%0A%20%20%20%20border%3A%20%7B%0A%20%20%20%20%20%20%20%20thin%3A%201px%2C%0A%20%20%20%20%20%20%20%20thick%3A%20999px%0A%20%20%20%20%7D%20solid%20%7B%0A%20%20%20%20%20%20%20%20light%3A%20%23FFF%2C%0A%20%20%20%20%20%20%20%20dark%3A%20%23000%2C%0A%20%20%20%20%20%20%20%20peach%3A%20pink%0A%20%20%20%20%7D%2C%0A%20%20%20%20shadow%3A%20(%0A%20%20%20%20%20%20%20%205px%205px%20blue%0A%20%20%20%20)(%0A%20%20%20%20%20%20%20%2010px%2010px%20red%0A%20%20%20%20)(%0A%20%20%20%20%20%20%20%2015px%2015px%20green%0A%20%20%20%20)%0A%7D%0A%0Adiv.border%20%7B%0A%20%20%20%20border%3A%20map.border%5B0%5D.thin%20map.border%5B1%5D%20map.border%5B2%5D.peach%0A%7D%0A%0Adiv.shadow%20%7B%0A%20%20%20%20box-shadow%3A%20unquote(join('%2C'%2C%20map.shadow))%0A%7D
