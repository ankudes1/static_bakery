Url shorten behavior
====================

by WyriHaximus on June 11, 2010

A while ago I started working on some twitter services and I needed an
automatic urlshortner. This first started as a model that would
shorten en url and save the result in a table for caching purposes.
After a good chat with a friend it turned into a behavior with the
model only using the behavior to act like it did before. One of the
reasons for a behavior was to create a simple interface for the
developer without the need to poll the caching model but at the same
time be able to use the cache model when the resulting short url
wouldnâ€™t be stored anywhere else (like an autmatic tweet). The model
currently supports 4 different services (I want to add more in the
future tho so any suggestions are welcome): 0x.tc, is.gd, bit.ly and
u.nu. (Taken from [url]http://blog.wyrihaximus.net/2010/04/cakephp-
url-shorten-behavior-0x-tc-bit-ly-is-gd-and-u-nu/[/url] as is most of
the article.)
Configuration

mode has 2 possible options ordered (default) or randomize. ordered
checks the order in order while randomize uses `shuffle`_ to randomize
the order.
Example:

::

    'mode' => 'ordered',

fields is an array with fields that the behavior should send to the
services to shorten the url (note that the value in the field MUST be
an url). This option has no default value!
Example:

::

    'fields' => array(
        'short'
    ),

order is an array with the services listed in the order to use. This
option wil only be used is mode is set to ordered else if mode is set
to randomize it will grab the default order array and overwrite what
you place in order .
Example:

::

    'order' => array(
                    'zero_xtc',
                    'bit_ly'
                    'u_nu',
                    'is_gd',
    ),

retries number of times it loops through the list of shortners before
giving up. Default value is 5.
Example:

::

    'retries' => 10,

shortners list with shortners and their options. All the shortners
have an enable option. Shortners that require an API key also have key
option to store the needed API key.
Example:

::

                'shortners' => array(
                    'bit_ly' => array(
                        'enable' => true,
                        // Go to http://bit.ly/account/your_api_key to get yours
                        'key' => array(
                            'login' => 'YOUR_BITLY_API_LOGIN_HERE',
                            'key' => 'YOUR_BITLY_API_KEY_HERE'
                        ),
                    ),
                ),

http_config is an array for the `Cakephp HttpSocket class`_. An
example an option is the time out, you can find all possible at the
`1.2 API`_ (or `1.3`_ ofcourse ;)). The default configuration is shown
in the example below.
Example:

::

                'http_config' => array(
                                'timeout' => 10
                ),

http_headers is an array for additional HTTP headers like the User-
Agent. The default configuration is shown in the example below.
Example:

::

                'http_headers' => array(
                                'User-Agent' => 'Mozilla/5.0 (Windows; U; Windows NT 6.0; en-US; rv:1.9.0.1) Gecko/2008070208 Firefox/3.0.1'
                ),

Files
Following are the mode and behavior. The model also shows the basic
configuration for the behavior tell it what field in the database to
shorten urls for.

The Model:

Model Class:
````````````

::

    <?php <?php
    class Shorturl extends AppModel {
        public $actsAs = array(
            'Shorturl' => array(
                'fields' => array(
                    'short'
                ),
            ),
        );
        public function shorten($long_url) {
            $url = $this->findByLong($long_url);
            if(!$url) {
                $data = array();
                $data['long'] = $long_url;
                $data['short'] = $long_url;
                $this->create();
                $this->save($data);
                $url = $this->findByLong($long_url);
            }
            return $url['Shorturl']['short'];
        }
    }?>

The create table SQL:

::

    CREATE TABLE IF NOT EXISTS `shorturls` (
      `id` int(32) NOT NULL auto_increment,
      `long` varchar(1024) NOT NULL,
      `short` varchar(255) NOT NULL,
      PRIMARY KEY  (`id`)
    ) TYPE=MyISAM  AUTO_INCREMENT=1 ;

The behavior:

Behavior Class:
```````````````

::

    <?php <?php
    class ShorturlBehavior extends ModelBehavior {
        private $defaults = array(
                'fields' => array(),
                'shortners' => array(
                                'zero_xtc' => array(
                                                'enable' => true,
                                ),
                                'is_gd' => array(
                                                'enable' => true,
                                ),
                                'bit_ly' => array(
                                                'enable' => true,
                                                // Go to http://bit.ly/account/your_api_key to get yours
                                                'key' => array(
                                                                'login' => '',
                                                                'key' => ''
                                                ),
                                ),
                                'u_nu' => array(
                                                'enable' => true,
                                ),
                ),
                'order' => array(
                                'zero_xtc',
                                'is_gd',
                                'bit_ly',
                                'u_nu',
                ),
                'http_config' => array(
                                'timeout' => 10
                ),
                'http_headers' => array(
                                'User-Agent' => 'Mozilla/5.0 (Windows; U; Windows NT 6.0; en-US; rv:1.9.0.1) Gecko/2008070208 Firefox/3.0.1'
                ),
                'retries' => 5,
                'mode' => 'ordered',
        );
        public $settings = array();
        public function setup(&$model,$config = array()) {
            $this->settings[$model->alias] = $this->defaults;
            $this->settings[$model->alias] = $this->array_merge_recursive_distinct($this->settings[$model->alias],(array) $config);
        }
        public function beforeSave(&$model) {
            if(isset($this->settings[$model->alias]['fields'])) {
                foreach($this->settings[$model->alias]['fields'] as $void => $field) {
                    if(isset($model->data[$model->alias][$field]) && !empty($model->data[$model->alias][$field]) && ((version_compare(phpversion(), '5.2.0', '>=') && function_exists('filter_var')) ? filter_var($model->data[$model->alias][$field], FILTER_VALIDATE_URL) : true)) {
                        $model->data[$model->alias][$field] = $this->shorten($model,$model->data[$model->alias][$field]);
                    }
                }
            }
            return true;
        }
        private function shorten(&$model,$long_url) {
            App::import('Core', 'Set');
            App::import('Core', 'Xml');
            App::import('Core', 'HttpSocket');
            if($this->settings[$model->alias]['mode']=='randomize') {
                $this->settings[$model->alias]['order'] = $this->defaults['order'];
                shuffle($this->settings[$model->alias]['order']);
            }
            for($j=0;$j<($this->settings[$model->alias]['retries'] + 1);$j++) {
                foreach($this->settings[$model->alias]['order'] as $shortner) {
                    if($this->settings[$model->alias]['shortners'][$shortner]['enable']) {
                        $short_url = $this->{$shortner}($model,$long_url);
                        if($short_url && !empty($short_url) && !is_null($short_url) && ((version_compare(phpversion(), '5.2.0', '>=') && function_exists('filter_var')) ? filter_var($short_url, FILTER_VALIDATE_URL) : true)) {
                            return $short_url;
                        }
                    }
                }
            }
            return $long_url;
        }
    
        private function zero_xtc(&$model,$long_url) {
            $this->Http = new HttpSocket($this->settings[$model->alias]['http_config']);
            $request = 'http://0x.tc/x?go=' . rawurlencode($long_url) . '&t=' . time();
            $response = $this->Http->get(
                    $request,
                    array(),
                    array('header' => $this->settings[$model->alias]['http_headers'])
            );
            $response = Set::reverse(new Xml($response));
            if(is_array($response['Taken']['xUrl'])) {
                return false;
            }
            elseif(substr($response['Taken']['xUrl'], 0, 4) == 'http') {
                return $response['Taken']['xUrl'];
            }
            else {
                return false;
            }
        }
    
        private function is_gd(&$model,$long_url) {
            $this->Http = new HttpSocket($this->settings[$model->alias]['http_config']);
            $request = 'http://is.gd/api.php?longurl=' . urlencode($long_url);
            $response = $this->Http->get(
                    $request,
                    array(),
                    array('header' => $this->settings[$model->alias]['http_headers'])
            );
            if (substr($request, 0, 4) == 'http') {
                return $response;
            }
            else {
                return false;
            }
        }
    
        private function bit_ly(&$model,$long_url) {
            if(isset($this->settings[$model->alias]['shortners']['bit_ly']['key']['login']) && !empty($this->settings[$model->alias]['shortners']['bit_ly']['key']['login']) && isset($this->settings[$model->alias]['shortners']['bit_ly']['key']['key']) && !empty($this->settings[$model->alias]['shortners']['bit_ly']['key']['key'])) {
                $this->Http = new HttpSocket($this->settings[$model->alias]['http_config']);
                $request = 'http://api.bit.ly/shorten?version=2.0.1&longUrl=' . urlencode($long_url) . '&login=' . $this->settings[$model->alias]['shortners']['bit_ly']['key']['login'] . '&apiKey=' . $this->settings[$model->alias]['shortners']['bit_ly']['key']['key'];
                $response = $this->Http->get(
                        $request,
                        array(),
                        array('header' => $this->settings[$model->alias]['http_headers'])
                );
                $response = json_decode($response);
                if ($response->errorCode==0 && $response->statusCode=='OK' && isset($response->results->{$long_url}->shortUrl)) {
                    return $response->results->{$long_url}->shortUrl;
                }
                else {
                    return false;
                }
            }
            else {
                return false;
            }
        }
    
        private function u_nu(&$model,$long_url) {
            $this->Http = new HttpSocket($this->settings[$model->alias]['http_config']);
            $request = 'http://u.nu/unu-api-simple?url=' . urlencode($long_url);
            $response = $this->Http->get(
                    $request,
                    array(),
                    array('header' => $this->settings[$model->alias]['http_headers'])
            );
            if (substr($request, 0, 4) == 'http') {
                return $response;
            }
            else {
                return false;
            }
        }
    
        // Taken from: http://www.php.net/manual/en/function.array-merge-recursive.php#96201
        /**
         * Merges any number of arrays / parameters recursively, replacing
         * entries with string keys with values from latter arrays.
         * If the entry or the next value to be assigned is an array, then it
         * automagically treats both arguments as an array.
         * Numeric entries are appended, not replaced, but only if they are
         * unique
         *
         * calling: result = array_merge_recursive_distinct(a1, a2, ... aN)
         **/
    
        private function array_merge_recursive_distinct () {
            $arrays = func_get_args();
            $base = array_shift($arrays);
            if(!is_array($base)) $base = empty($base) ? array() : array($base);
            foreach($arrays as $append) {
                if(!is_array($append)) $append = array($append);
                foreach($append as $key => $value) {
                    if(!array_key_exists($key, $base) && !is_numeric($key)) {
                        $base[$key] = $append[$key];
                        continue;
                    }
                    if(is_array($value) || (isset($base[$key]) && is_array($base[$key]))) {
                        $base[$key] = $this->array_merge_recursive_distinct($base[$key], $append[$key]);
                    } else if(is_numeric($key)) {
                        if(!in_array($value, $base)) $base[] = $value;
                    } else {
                        $base[$key] = $value;
                    }
                }
            }
            return $base;
        }
    }?>



.. _1.2 API: http://api12.cakephp.org/view_source/http-socket/#l-111
.. _Cakephp HttpSocket class: http://api12.cakephp.org/class/http-socket
.. _1.3: http://api13.cakephp.org/view_source/http-socket/#l-114
.. _shuffle: http://php.net/shuffle
.. meta::
    :title: Url shorten behavior
    :description: CakePHP Article related to behavior,isgd,zeroxtc,bitly,unu,Behaviors
    :keywords: behavior,isgd,zeroxtc,bitly,unu,Behaviors
    :copyright: Copyright 2010 WyriHaximus
    :category: behaviors

