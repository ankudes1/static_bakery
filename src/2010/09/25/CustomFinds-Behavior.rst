CustomFinds Behavior
====================

Behavior that enables you to config custom finds at Model in order to
use with Model->find();

Behavior that enables you to config custom finds at Model in order to
use with Model->find();


Installation
~~~~~~~~~~~~
- You can download from Github:
`http://github.com/mobvox/CustomFinds`_

Model Class:
````````````

::

    <?php 
    class Product extends AppModel{
        //...
        var $actsAs = array('CustomFinds.CustomFinds');
    
        var $customFinds = array(
            'topSellers' => array(
                'fields' => array('Product.name','Product.price', ...),
                'contain' => array('ProductImage.source'),
                'conditions' => array('Product.countSeller >' => 20, 'Product.is_active' => 1),
                'recursive' => 1,
                //All others find options
            )
        );
        //...
    }
    ?>


Controller Class:
`````````````````

::

    <?php 
    class ProductsController extends AppController{
        //...
        var $paginate = array(
            'custom' => 'topSellers',
            //'conditions' => array(...),
            //...
        );
        function index(){
            $findAll = $this->Product->find('all', array('custom' => 'topSellers', 'conditions' => array('Product.category_id' => 2)));
            $findFirst = $this->Product->find('first', array('custom' => 'topSellers'));
            $findCount = $this->Product->find('count', array('custom' => 'topSellers'));
        }
        //...
    } 
    ?>

Edited: Packaged as plugin.

.. _http://github.com/mobvox/CustomFinds: http://github.com/mobvox/CustomFinds

.. author:: Danielpk
.. categories:: articles, behaviors
.. tags:: find,behavior,custom,custom query,custom find,Behaviors

