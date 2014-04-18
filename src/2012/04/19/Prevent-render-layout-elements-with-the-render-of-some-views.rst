Prevent render layout elements with the render of some views!
=============================================================

This snippet is meant by the situation in-which you want to prevent
some elements in the layout from render while some views is rendered.

> ### Updated ###

> In the conditional statement:

>

::

    if (isset($this->preventElement)...

> $this->preventElement becomes $this->preventElement[$e[1]], to solve
missing index error generated when using this snippet with more than
one element in the layout.

Consider the following example, there is an element that displays the
top searched keywords and it is named top_searches.ctp. This element
is placed in the layout to be rendered under the search box element.

Suppose now, we have an index view that lists all searched keywords
and we don't want to make the top_searches elements to be rendered
while we call the index view.

We should add the following code snippet into the most upper line of
top_searches, i.e line 1.

::

    <?php
    $e = explode('views/elements/',__FILE__);
    $e[1] = str_replace('.ctp', '', $e[1]);
    if (isset($this->preventElement[$e[1]]) && $e[1] == $this->preventElement[$e[1]]) return false;
    ?>

Simply the above code extracts the path and name of the element's ctp
file relative to the elements directory, then, it asks for an array
property of the View object called preventElement, if it find the name
of the element is set as a key valued by its name too, it will return
false to prevent the rest of the element to be rendered.

Now in the index.ctp we should add the following code:

::

    <?php $this->preventElement['top_searches'] = 'top_searches';?>

I placed the above line of code on the first line too, it is,
obviously, that we defined the preventElement property and we set the
name of the element we want to stop or prevent its render.



.. author:: saidbakr
.. categories:: articles, snippets
.. tags:: view,layout,elements,render,prevent,Snippets

