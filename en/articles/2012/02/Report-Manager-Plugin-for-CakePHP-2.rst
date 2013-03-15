Report Manager Plugin for CakePHP 2
===================================

by %s on February 23, 2012

The report manager plugin can help users to create reports based on
application models.

Changelog for version 0.4.5

+ bugfix: delete saved report does not refresh the list properly
+ bugfix: array index errors on loading reports

Changelog for version 0.4.4

+ enhancement: JS changes to work on non-root URLs

Changelog for version 0.4.3

+ bugfix: default.js : update after renumber position

Changelog for version 0.4.2

+ bugfix: order.ctp - test if OrderBy1 and OrderBy2 are set
+ bugfix: ReportsController listReports method - handle empty array

Changelog for version 0.4

+ Load and Save reports
+ Export to XLS

Changelog for version 0.3

+ One to many reports
+ Sortable fields by drag and drop ( step 1 )
+ Click to add field change background color ( step 1 )
+ Click in model name check/uncheck all fields ( step 1 )
+ SmartWizard validation ( step 1 )
+ Datepicker for date fields ( step 2 )
+ Checkbox to enable counter option ( step 4 )
+ Check box for one to many reports : show items with no related
  records ( step 4 )
+ Both jquery and jquery ui libraries are loaded from google

Installation

#. Download the plugin from github or sourceforge ( where you can see
   some screenshots )

`https://sourceforge.net/projects/repomancakephp/`_

`https://github.com/luisdias/CakePHP-Report-Manager-Plugin`_

#. Extract the zip file on the app/Plugin folder ( the plugin folder
   must be named ReportManager )
#. Add the following line to your bootstrap.php file ( located at
   app/Config folder )

CakePlugin::load('ReportManager',array('bootstrap' => true));

#. Go to the url `http://mycakeapp/report_manager/reports`_ to see the
   main page listing all models

Using the plugin

The wizard interface is self explanatory.

#. On the first tab you can select the fields and their position
#. On the second tab you can define a filter
#. On the third tab you can select up to two fields to sort
#. On the last tab you can enter a name for your report and choose
   between 5 style options

Configuration:

Some parameters could be configured in the
app/Plugin/ReportManager/Config/bootstrap.php

+ Display foreign keys
+ Ignore List for global fields ( affects all models )
+ Ignore list for models
+ Ignore list for specific model's fields
+ Reports directory path

Notes:

It was inspired by the Report Creator Component by Gene Kelly from Nov
9th 2006.

`http://bakery.cakephp.org/articles/Gkelly/2006/11/09/report-creator-
component`_

It also uses a Jquery plugin called SmartWizard by Tech Laboratory.

`http://techlaboratory.net/products.php?product=smartwizard`_

Since version 0.3 the Report Manager Plugin load the jQuery and jQuery
UI libraries from Google

Collaborators:

Suman (USA)

Santana (Brazil)

Tamer Solieman (Egypt)

jasonchua89

Author: Luis E. S. Dias

Contact: smartbyte.systems@gmail.com


.. _https://github.com/luisdias/CakePHP-Report-Manager-Plugin: https://github.com/luisdias/CakePHP-Report-Manager-Plugin
.. _http://mycakeapp/report_manager/reports: http://mycakeapp/report_manager/reports
.. _http://techlaboratory.net/products.php?product=smartwizard: http://techlaboratory.net/products.php?product=smartwizard
.. _https://sourceforge.net/projects/repomancakephp/: https://sourceforge.net/projects/repomancakephp/
.. _http://bakery.cakephp.org/articles/Gkelly/2006/11/09/report-creator-component: http://bakery.cakephp.org/articles/Gkelly/2006/11/09/report-creator-component
.. meta::
    :title: Report Manager Plugin for CakePHP 2
    :description: CakePHP Article related to plugin,report,cakephp2,manager,Plugins
    :keywords: plugin,report,cakephp2,manager,Plugins
    :copyright: Copyright 2012 
    :category: plugins

