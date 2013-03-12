

Display TYPO3 content using CakePHP
===================================

by %s on September 22, 2010

Sometimes using TypoScript to build a website frontend is not an
option. Reasons might be that you want to avoid learning this
specialized configuration language. Or you might want to combine a
TYPO3 backend with an existing frontend implementation. For these
cases, we have created a base CakePHP implementation to access the
TYPO3 tables "pages" and "tt_content" and display its content in a
CakePHP generated frontend.
Access the software at `http://schettler.net/fossil/t3c`_ and take
part in this exciting new way to build TYPO3 backed websites.

The software has the following properties

+ No TypoScript used
+ Can display almost all content types from the TYPO3 demo
  installation, using standard CakePHP view elements. The set of content
  types supported is continuously being updated. Patches welcome!
+ Clean, SEO, optimized URLs out of the box. No TYPO3 extension like
  eg. realurl needs to be installed.
+ All CakePHP mechanisms usable, eg. user authentication, caching,
  themes. Patches, compatible plugins, or themes welcome!



.. _http://schettler.net/fossil/t3c: http://schettler.net/fossil/t3c
.. meta::
    :title: Display TYPO3 content using CakePHP
    :description: CakePHP Article related to content management,typo,Snippets
    :keywords: content management,typo,Snippets
    :copyright: Copyright 2010 
    :category: snippets

