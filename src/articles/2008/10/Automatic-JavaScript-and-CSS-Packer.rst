Automatic JavaScript and CSS Packer
===================================

by mattc on October 26, 2008

Another JavaScript/CSS combiner and packer was recently posted to the
Bakery. I felt it was a little configuration heavy and required too
much manual intervention. This version is much more automated and
requires virtually no code changes.


Notes
`````

+ JavaScript packing only works with PHP5. If you're using PHP4 the
  scripts will still be merged into one file, but not packed.
+ A sample .htaccess file is included in the zip. Rename it and drop
  it in js/packed and css/packed for improved performance.



Download
````````
A zip of the code and .htaccess can be found here:
`http://github.com/mcurry/cakephp/tree/master/helpers/asset`_, as well
as posted below.


Instructions
````````````

#. You'll need a working version of CakePHP 1.2 installed.
#. Download jsmin 1.1.0 (`http://code.google.com/p/jsmin-php/`_) or
   later and put it in vendors/jsmin.
#. Download CSSTidy 1.3 (`http://csstidy.sourceforge.net/`_) or later
   and put the contents in vendors/csstidy.
#. Download/copy+paste the helper and unzip it to app/views/helpers.
#. Include the helper in any controller that will need it. Most likely
   you will put it in AppController so that it's available to all your
   controllers:



Tips
````

+ Remember to set the inline option to false for JS and CSS in your
  layout if you want them to be packed with the view scripts.
+ Setting DEBUG on will cause this helper to output the scripts the
  same way $scripts_for_layout would, effectifly turning it off while
  testing.
+ If you get a JavaScript error with a packed version of a file it's
  most likely missing a semi-colon somewhere.
+ Order is important. If you include script1 then script2 on one view
  and script2 then script1 on another, they will generate separate
  packed versions and will be treated by the browser as separate
  scripts.



Helper Class:
`````````````

::

    <?php 
    /*
     * Asset Packer CakePHP Component
     * Copyright (c) 2008 Matt Curry
     * www.PseudoCoder.com
     * http://www.pseudocoder.com/archives/2007/08/08/automatic-asset-packer-cakephp-helper
     *
     * @author      mattc <matt@pseudocoder.com>
     * @version     1.2
     * @license     MIT
     *
     */
    
    class AssetHelper extends Helper {
      //Cake debug = 0                          packed js/css returned.  $this->debug doesn't do anything.
      //Cake debug > 0, $this->debug = false    essentially turns the helper off.  js/css not packed.  Good for debugging your js/css files.
      //Cake debug > 0, $this->debug = true     packed js/css returned.  Good for debugging this helper.
      var $debug = false;
    
      //there is a *minimal* perfomance hit associated with looking up the filemtimes
      //if you clean out your cached dir (as set below) on builds then you don't need this.
      var $checkTS = true;
    
      //the packed files are named by stringing together all the individual file names
      //this can generate really long names, so by setting this option to true
      //the long name is md5'd, producing a resonable length file name.
      var $md5FileName = false;
    
      //you can change this if you want to store the files in a different location.
      //this is relative to your webroot/js and webroot/css paths
      var $cachePath = 'packed/';
    
      //set the css compression level
      //options: default, low_compression, high_compression, highest_compression
      //default is no compression
      //I like high_compression because it still leaves the file readable.
      var $cssCompression = 'high_compression';
    
      var $helpers = array('Html', 'Javascript');
      var $viewScriptCount = 0;
    
      //flag so we know the view is done rendering and it's the layouts turn
      function afterRender() {
        $view =& ClassRegistry::getObject('view');
        $this->viewScriptCount = count($view->__scripts);
      }
    
      function scripts_for_layout() {
        $view =& ClassRegistry::getObject('view');
    
        //nothing to do
        if (!$view->__scripts) {
          return;
        }
    
        //move the layout scripts to the front
        $view->__scripts = array_merge(
                             array_slice($view->__scripts, $this->viewScriptCount),
                             array_slice($view->__scripts, 0, $this->viewScriptCount)
                           );
    
    
        if (Configure::read('debug') && $this->debug == false) {
          return join("\n\t", $view->__scripts);
        }
    
        //split the scripts into js and css
        foreach ($view->__scripts as $i => $script) {
          if (preg_match('/js\/(.*).js/', $script, $match)) {
            $temp = array();
            $temp['script'] = $match[1];
            $temp['name'] = basename($match[1]);
            $js[] = $temp;
    
            //remove the script since it will become part of the merged script
            unset($view->__scripts[$i]);
          } else if (preg_match('/css\/(.*).css/', $script, $match)) {
            $temp = array();
            $temp['script'] = $match[1];
            $temp['name'] = basename($match[1]);
            $css[] = $temp;
    
            //remove the script since it will become part of the merged script
            unset($view->__scripts[$i]);
          }
        }
    
        $scripts_for_layout = '';
        //first the css
        if (!empty($css)) {
          $scripts_for_layout .= $this->Html->css($this->cachePath . $this->process('css', $css));
          $scripts_for_layout .= "\n\t";
        }
    
        //then the js
        if (!empty($js)) {
          $scripts_for_layout .= $this->Javascript->link($this->cachePath . $this->process('js', $js));
        }
    
        //finally anything that was left over, usually codeBlocks
        $scripts_for_layout .= join("\n\t", $view->__scripts);
    
        return $scripts_for_layout;
      }
    
    
      function process($type, $data) {
        switch ($type) {
          case 'js':
            $path = JS;
            break;
          case 'css':
            $path = CSS;
            break;
        }
    
        $folder = new Folder;
    
        //make sure the cache folder exists
        if ($folder->create($path . $this->cachePath, "777")) {
          trigger_error('Could not create ' . $path . $this->cachePath
                        . '. Please create it manually with 777 permissions', E_USER_WARNING);
        }
    
        //check if the cached file exists
        $names = Set::extract($data, '{n}.name');
        $folder->cd($path . $this->cachePath);
        $fileName = $folder->find($this->__generateFileName($names) . '_([0-9]{10}).' . $type);
        if ($fileName) {
          //take the first file...really should only be one.
          $fileName = $fileName[0];
        }
    
        //make sure all the pieces that went into the packed script
        //are OLDER then the packed version
        if ($this->checkTS && $fileName) {
          $packed_ts = filemtime($path . $this->cachePath . $fileName);
    
          $latest_ts = 0;
          $scripts = Set::extract($data, '{n}.script');
          foreach($scripts as $script) {
            $latest_ts = max($latest_ts, filemtime($path . $script . '.' . $type));
          }
    
          //an original file is newer.  need to rebuild
          if ($latest_ts > $packed_ts) {
            unlink($path . $this->cachePath . $fileName);
            $fileName = null;
          }
        }
    
        //file doesn't exist.  create it.
        if (!$fileName) {
          $ts = time();
    
          //merge the script
          $scriptBuffer = '';
          $scripts = Set::extract($data, '{n}.script');
          foreach($scripts as $script) {
            $buffer = file_get_contents($path . $script . '.' . $type);
    
            switch ($type) {
              case 'js':
                //jsmin only works with PHP5
                if (PHP5) {
                  vendor('jsmin/jsmin');
                  $buffer = trim(JSMin::minify($buffer));
                }
                break;
    
              case 'css':
                vendor('csstidy/class.csstidy');
                $tidy = new csstidy();
                $tidy->load_template($this->cssCompression);
                $tidy->parse($buffer);
                $buffer = $tidy->print->plain();
                break;
            }
    
            $scriptBuffer .= "\n/* $script.$type */\n" . $buffer;
          }
    
    
          //write the file
          $fileName = $this->__generateFileName($names) . '_' . $ts . '.' . $type;
          $file = new File($path . $this->cachePath . $fileName);
          $file->write(trim($scriptBuffer));
        }
    
        if ($type == 'css') {
          //$html->css doesn't check if the file already has
          //the .css extension and adds it automatically, so we need to remove it.
          $fileName = str_replace('.css', '', $fileName);
        }
    
        return $fileName;
      }
    
      function __generateFileName($names) {
        $fileName = str_replace('.', '-', implode('_', $names));
    
        if ($this->md5FileName) {
          $fileName = md5($fileName);
        }
    
        return $fileName;
      }
    }
    ?>



.. _http://github.com/mcurry/cakephp/tree/master/helpers/asset: http://github.com/mcurry/cakephp/tree/master/helpers/asset
.. _http://csstidy.sourceforge.net/: http://csstidy.sourceforge.net/
.. _http://code.google.com/p/jsmin-php/: http://code.google.com/p/jsmin-php/
.. meta::
    :title: Automatic JavaScript and CSS Packer
    :description: CakePHP Article related to packer,asset,Helpers
    :keywords: packer,asset,Helpers
    :copyright: Copyright 2008 mattc
    :category: helpers

