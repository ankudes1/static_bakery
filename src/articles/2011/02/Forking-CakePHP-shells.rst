Forking CakePHP shells
======================

by webjay on February 21, 2011

For `Gignal`_ I needed a way to have bots running in the background.
Usually I would do something like `exec('command >& /dev/null &');`
but I stumpled upon Process Control in Php's manual and here is what I
found out.
`
privatefunctionstart_bot($id){
$pid=pcntl_fork();
if($pid==-1){//forkerror
returnfalse;
}elseif($pid){//wearetheparent
return$pid;
}else{//wearethechild
//reconnectDB
$db=&ConnectionManager::getDataSource('default');
$db->reconnect();
//startbot.
//do_stuff()canbeusedtodobackgroundstuff.
$this->do_stuff($id);
}
}
`

.. _Gignal: http://gignal.com/
.. meta::
    :title: Forking CakePHP shells
    :description: CakePHP Article related to fork,bot,Tutorials
    :keywords: fork,bot,Tutorials
    :copyright: Copyright 2011 webjay
    :category: tutorials

