This is a complement of session files.

When you starts an activity thorugh ajax request and you want get info of this activity via ajax, 
the subsequents requests are blocked waiting for first request finish.

You need to stop the use of php session file to serve more ajax request.

Sample of use:

Status.php
==========

<?php
    if (isset($_REQUEST['sid'])) session_id($_REQUEST['sid']);
    session_start();
    
    // MySession
    require '../MySession/MySession.php';
    $mysession = new MySession(session_id());
    
    $id = isset($_REQUEST['id'])?$_REQUEST['id']:'';
    
   
    // this sends to requester the percent of activiy and id,
    // normally the requester is a javascript routine and for not
    // use global id vars now this script returns id
    print(json_encode(array(
                        "p"  => $mysession->readKey($id),
                        "id" => $id,
         )));
    
        
?>



Activity
========
This file is that starts the activity and controls the percentage of activity,
sample:

<?php

function process_demo(){

    // ..code..

    $mysession = new MySession( session_id() );
    
    $app->contentType("text/html");
    
    $mysession->writeKey($id, 0);
    
    // relase php session for calls from requester stay attended
    session_write_close();
    
    // .. code that process long time ..
    
    // indicates that finishes the long action
    $mysession->writeKey($id, "OK");
    
    die();    
}

?>



In index.php from Slim major index add:


// MySession
require './app/others/MySession/MySession.php';  