==================
コンフィグファイル
==================

ちいたんではコントローラーのアクションを実行する前に、フレームワークからいくつかの設定用関数が呼ばれます。 
それによりデータベースやモデル、セッションといった機能の設定を行います。 

ユーザーが設定関数を宣言していなかった場合、デフォルトの動作が行われるため必要なければ設定を行わなくても動作します。

関数宣言
========

設定関数は ``config.php`` ファイルにまとめて宣言することを推奨しています。 
各々のページで ``cheetan.php`` をrequireする前に ``config.php`` をrequireします。

特定のページのみに別の動作を行わせたい時は ``config.php`` 内の関数を以下のように宣言します。 
そして、ページでは ``config.php`` をrequireする前に関数を宣言します。

**config.php**

.. code-block:: php

    <?php
    if ( !function_exists( 'config_function' ) ) {
        function config_function()
        {
            return true;
        }
    }

**onepage.php**

.. code-block:: php

    <?php
    function config_function()
    {
        return false;
    }
    require_once 'config.php';
    require_once 'cheetan.php';


設定関数
========

* function is_session()
* function config_controller_class()
* function config_view_class()
* function config_database( CDatabase &db )
* function config_models( CController &controller )
* function config_components( CController &controller )
* function is_secure( CController &controller )
* function check_secure( CController &controller )
* function config_controller( CController &controller )
* function after_action( CController &controller )
* function after_render( CController &controller )


**function is_session()**

セッションを使用するかどうかを設定します。 デフォルト(関数を宣言しない時)はセッションを使用します。 使用したくない時は関数を宣言し、 ``FALSE`` を返してください。

.. code-block:: php

    <?php
    function is_session()
    {
        return FALSE;
    }

**function config_controller_class()**

コントローラクラスをオーバーライドしたい時に宣言します。

.. code-block:: php

    <?php
    function config_controller_class()
    {
        class CMyController extends CController
        {

        }
        return 'CMyController';
    }

**function config_view_class()**

ビュークラスをオーバーライドしたい時に宣言します。

.. code-block:: php

    <?php
    function config_view_class()
    {
        class CMyView extends CView
        {

        }
        return 'CMyView';
    }

**function config_database( CDatabase &db )**

データベースの設定を行います。 以下の関数を利用してデータベースを設定します。

.. code-block:: php

    <?php
    function CDatabase::add( string settingname, string host, string user, string password, string dbname )

``host`` 以降は特に説明は必要ないでしょう。 ``settingname`` は複数の接続を利用したい時の識別子です。 モデルやデータベースのクエリー関数で指定できます。 特に一つしか使わない場合は名前を指定しないとデフォルトの設定として呼び出されます。

.. code-block:: php

    <?php
    function config_database( &$db )
    {
        $db->add( '', 'localhost', 'user', 'password', 'dbname' );
        $db->add( 'special', '192.168.0.100', 'user', 'password', 'dbname' );
    }

**function config_models( CController &controller )**

コントローラーにモデルを読み込みます。 以下の関数を利用してモデルの設定を行います。

.. code-block:: php

    <?php
    function CController::AddModel( string filepath [, string name ] )

``filepath`` はモデルを宣言してあるファイルを指定します。 
デフォルトではファイル名の一文字目のみを大文字にして頭にCをつけたクラスを宣言します（ ``user.php`` の場合 ``CUser`` ）。 そしてファイル名と同じテーブルを参照します。

通常コントローラの$m配列とメンバ変数、ファイル名と同じ名前で保存されます。 ``name`` を指定することによってその名前に変更できます。

.. code-block:: php

    <?php
    function config_models( &$controller )
    {
        $controller->db->query( 'SET NAMES ujis' );	//if you need
        $controller->AddModel( dirname( __FILE__ ) . '/models/user.php' );
    }

**function config_components( CController &controller )**

コントローラーにコンポーネントを読み込みます。 以下の関数を利用してコンポーネントの設定を行います。

.. code-block:: php

    <?php
    function CController::AddComponent( string filepath [, string cname [, string name ] ] )

``filepath`` はコンポーネントを宣言してあるファイルを指定します。
デフォルトではファイル名の一文字目のみを大文字にして頭にCをつけたクラスを宣言します（ ``user.php`` の場合 ``CUser`` ）。

そしてファイル名と同じテーブルを参照します。 クラス名は ``cname`` によって変更できます。
また、 ``name`` を指定すれば ``$c[name]`` とコントローラの ``name`` 変数に保存するよう指定できます。

.. code-block:: php

    <?php
    function config_components( &$controller )
    {
        $controller->AddComponent( dirname( __FILE__ ) . '/components/mail.php' );
        $controller->AddComponent( 'Smarty/Smarty.class.php', 'Smarty', 'smarty' );
        $controller->SetViewExt( '.tpl' );
    }


**function is_secure( CController &controller )**
**function check_secure( CController &controller )**

ユーザー認証用の設定関数 ``check_secure`` を呼び出すかどうかを ``is_secure`` で設定します。
デフォルト ( ``is_secure`` 関数を宣言しない時）は ``check_secure`` は呼び出されません。
認証を行いたい時は ``TRUE`` を返します。

.. code-block:: php

    <?php
    function is_secure( &$controller )
    {
        return TRUE;
    }

    function check_secure( &$controller )
    {
        if ( empty( $_SESSION['user'] ) ) {
            $controller->redirect( 'login.php' );
        }
    }



**function config_controller( CController &controller )**

アクションが呼ばれる直前に呼ばれる関数です。 特に何をするべきという関数ではありませんので必要に応じて使用します。 下記サンプルでは共通テンプレートファイルを設定しています。

.. code-block:: php

    <?php
    function config_controller( &$controller )
    {
        $controller->SetTemplateFile( 'template.html' );
    }

**function after_action( CController &controller )**

アクションが呼ばれた直後に呼ばれる関数です。 特に何をするべきという関数ではありませんので必要に応じて使用します。 ``smarty`` で出力を行っています。

.. code-block:: php

    <?php
    function after_action( &$controller )
    {
        $view = $controller->GetViewFile();
        $controller->smarty->display( $view );
        exit();
    }

**function after_render( CController &controller )**

ビューが呼ばれた直後に呼ばれる関数です。 特に何をするべきという関数ではありませんので必要に応じて使用します。