AJAX Helper with jQuery (for CakePHP 2.x)
=========================================

by jozek000 on November 23, 2011

New version of my `AJAX Helper with jQuery`_ (for CakePHP 2.x) has
arrived.
Check it out on my site.
Hi!
On my site you can find `AJAX Helper with jQuery`_ for new version of
CakePHP (2.x).

All functions that were introduced in last version are supported and
compatibile with CakePHP 2.0.

Actual function list:

+ link - Returns a link to a remote action that's called in the
  background using XMLHttpRequest when the link is clicked. The result
  of that request can then be inserted into a DOM object
+ remoteFunction - This function creates the JavaScript needed to make
  a remote call. It is primarily used as a helper for link(). This is
  not used very often unless you need to generate some custom scripting.
+ remoteTimer - Periodically calls the action. Usually used to update
  a specific div with the result of the remote call.
+ form - Returns a form tag that submits using XMLHttpRequest instead
  of a normal HTTP request via post or get. Otherwise, form submission
  will behave exactly like normal: data submitted is available at
  $this->data inside your controllers.
+ submit - Returns a submit button that submits the form and updates
  specified div
+ observeField - Observes the field and makes an XMLHttpRequest when
  its contents have changed.
+ observeForm - Similar to observeField(), but operates on an entire
  form identified by the DOM.
+ autoComplete - Renders a text field with autocomplete. The remote
  action should return a suitable list of autocomplete terms.
+ drag - Makes a Draggable element out of the DOM element.
+ drop - Makes the DOM element able to accept dropped elements.
+ dropRemote - Makes a drop target that creates an XMLHttpRequest when
  a draggable element is dropped on it.
+ slider - Creates a directional slider control.
+ editor - Creates an in-place editor at DOM element.
+ sortable - Makes a list or group of floated objects contained in one
  sortable container
+ selectable - Allows for elements to be selected by dragging a box
  (sometimes called a lasso) with the mouse over the elements.
+ resizable - Makes selected element resizable (meaning they have
  draggable resize handles)
+ accordion - Make the selected elements Accordion widgets.
+ button - Button enhances standard form elements like button, input
  of type submit or reset or anchors to themable buttons with appropiate
  mouseover and active styles.
+ datepicker - Adds datepicker functionality to your pages. You can
  customize the date format and language, restrict the selectable date
  ranges and add in buttons and other navigation options easily.
+ dialog - A dialog is a floating window that contains a title bar and
  a content area. The dialog window can be moved, resized and closed
  with the 'x' icon by default.
+ progressbar - The progress bar is designed to simply display the
  current % complete for a process. The bar is coded to be flexibly
  sized through CSS and will scale to fit inside it's parent container
  by default.
+ tabs - are generally used to break content into multiple sections
  that can be swapped to save space, much like an accordion.

I think it's worth a while.

This Helper is replacement for old orginal CakePHP AjaxHelper, but
it's not fully compatible with orginal Helper. Most of differences can
be found in $options parameter (because of using other library).

Contact me if you find any problems.

.. _AJAX Helper with jQuery: http://www.cakephp.4uk.pl/
.. meta::
    :title: AJAX Helper with jQuery (for CakePHP 2.x)
    :description: CakePHP Article related to javascript,AJAX,autocomplete,jquery,sortable,date picker,jqueryui,ui,dialog,user interface,progressbar,slider,Helpers
    :keywords: javascript,AJAX,autocomplete,jquery,sortable,date picker,jqueryui,ui,dialog,user interface,progressbar,slider,Helpers
    :copyright: Copyright 2011 jozek000
    :category: helpers

