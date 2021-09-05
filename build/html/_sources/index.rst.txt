=======================================
ちいたん ユーザガイド Version |release|
=======================================

ちいたんへようこそ
==================
ちいたんはMITライセンスで配布されている **世界最軽量** のPHP用MVCフレームワークです。

ちいたんの特徴
==============
最小ソースコードはこれだけです。

*１ファイルの場合*

.. code-block:: php

   ----hello.php----
   <?php
   require_once 'cheetan.php';

   function action( &$c )
   {
      $c->set( 'msg', 'Hello, World!' );
   }
   ?>
   <html>
   <body>
   <h1><?php echo $data['msg'];?></h1>
   </body>
   </html>


*テンプレート使用の場合*

.. code-block:: php

   ----hello.php----
   <?php
      require_once 'cheetan.php';

   function action( &$c )
   {
      $c->set( 'msg', 'Hello, World!' );
   }
   ?>


   ----hello.html----
   <html>
   <body>
   <h1><?php echo $data['msg'];?></h1>
   </body>
   </html>



テンプレートも使えます。

*１ファイルの場合*

.. code-block:: php

   ----temp.html----
   <html>
   <body>
   <?php contents( $data );?>
   </body>
   </html>


   ----hello.php----
   <?php
      require_once 'cheetan.php';

   function action( &$c )
   {
      $c->SetTemplateFile( 'temp.html' );
      $c->set( 'msg', 'Hello, World!' );
   }

   function contents( $data )
   {
   ?>
   <h1><?php echo $data['msg'];?></h1>
   <?php
   }
   ?>


*テンプレート使用の場合*

.. code-block:: php

   ----temp.html----
   <html>
   <body>
   <?php $this->content();?>
   </body>
   </html>


   ----hello.php----
   <?php
      require_once 'cheetan.php';

   function action( &$c )
   {
      $c->SetTemplateFile( 'temp.html' );
      $c->set( 'msg', 'Hello, World!' );
   }
   ?>


   ----hello.html----
   <h1><?php echo $data['msg'];?></h1>


モデルも完備。

.. code-block:: php

   ----config.php----
   function config_database( &$db )
   {
      $db->add( '', 'localhost', 'user', 'password', 'db' );
   }

   function config_models( &$controller )
   {
      $controller->AddModel( 'user.php' );
   }


   ----user.php----
   class CUser extends CModel
   {

   }


   ----test.php----
   <?php
      require_once 'config.php';
      require_once 'cheetan.php';

   function action( &$c )
   {
      if ( count( $_POST ) ) {
         $c->user->insert( $c->data['user'] );
      }
   }
   ?>
   <form method="post" action="test.php">
   名前<br />
   <input type="text" name="user/name" /><br />
   メールアドレス<br />
   <input type="text" name="user/email" />
   </form>


.. toctree::
   :maxdepth: 1
   :glob:
   :caption: インストール

   download
   install

.. toctree::
   :maxdepth: 1
   :glob:
   :caption: マニュアル

   tutorial
   configfile
   model
   view
   controller
   database
   validate
   sanitize
   logging
   notice

.. toctree::
   :maxdepth: 1
   :glob:
   :caption: 機能拡張例

   controller-extend
   view-extend
   component
   actioncontroller

