============
コントローラ
============

関数
====

* bool AddModel( string path [, string name ] )
* bool AddComponent( string path [, string cname [, string name ]] )
* void SetTemplateFile( string template )
* void SetViewFile( string viewfile )
* void SetViewPath( string viewpath )
* void SetViewExt( string ext )
* void set( string name, mixed value )
* void setarray( mixed datas )
* void redirect( string url [, bool is301 ] )
* void SetDebug( bool debug )


**bool AddModel( string path [, string name ] )**

モデルを登録します。

.. code-block:: php

    <?php
    function config_models( &$controller )
    {
        $controller->AddModel( 'model.php' );
    }


**bool AddComponent( string path [, string cname [, string name ]] )**

コンポーネントを登録します。

.. code-block:: php

    <?php
    function config_components( &$controller )
    {
        $controller->AddComponent( 'component.php' );
    }


**void SetTemplateFile( string template )**

テンプレートファイルを登録します。

.. code-block:: php

    <?php
    function config_controller( &$controller )
    {
        $controller->SetTemplateFile( 'template.html' );
    }


**void SetViewFile( string viewfile )**

ビューに使用するファイルを指定します。 標準設定はコントローラファイルの拡張子を ``.html`` としたファイルです。  ``action`` 関数で使用します。

.. code-block:: php

    <?php
    function action( &$c )
    {
        $c->SetViewFile( 'index_.html' );
    }


**void SetViewPath( string viewpath )**

ビューファイルが保存されているパスを登録します。 標準設定はコントローラファイルと同一のディレクトリです。

.. code-block:: php

    <?php
    function config_controller( &$controller )
    {
        $controller->SetViewPath( 'views/' );
    }


**void SetViewExt( string ext )**

ビューファイルの拡張子を登録します。 標準設定は ``.html`` です。

.. code-block:: php

    <?php
    function config_controller( &$controller )
    {
        $controller->SetViewExt( '.tpl' );
    }


**void set( string name, mixed value )**

ビューに変数を渡します。

.. code-block:: php

    <?php
    function action( &$c )
    {
        $c->set( 'msg', 'メッセージ' );
    }
    ?>


**void setarray( mixed datas )**

ビューに変数を配列で渡します。引き渡す配列のキーで個々の変数にアクセスします。

.. code-block:: php

    <?php
    function action( &$c )
    {
        $c->set( $msgs );
    }
    ?>


**void redirect( string url [, bool is301 ] )**

別のページへ転送します。 標準（ ``is301=FALSE`` ）は302 Moved Temporarily（一時的な移転）で転送します。 
``is301`` を ``TRUE`` にすると301 Moved Permanently（恒久的な移転）で転送します。

.. code-block:: php

    <?php
    function action( &$c )
    {
        $c->redirect( 'index.php' );
    }
    ?>


**void SetDebug( bool debug )**

デバッグモードを指定します。 ``TRUE`` を指定するとデバッグモードになります。

.. code-block:: php

    <?php
    function config_controller( &$controller )
    {
        $controller->SetDebug( TRUE );
    }
    