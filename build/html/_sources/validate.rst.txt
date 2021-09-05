==========
バリデート
==========

フォームなどから取得したデータを検証します。 コントローラにはバリデータが装備されていて、モデルとバリデータの連結によって非常に簡単にバリデートを行うことができます。
バリデートを行うには以下の方法があります。

* モデルと連携させて複数データを自動的に行う
* データを個々に検証


モデルと連携させてバリデート
============================

モデルの ``$validatefunc`` 変数にバリデート関数を指定しておくことにより、モデルの ``validate`` 関数で一括検証を行います。 例えば以下のように宣言しておきます。

**user.php**

.. code-block:: php

    <?php
    class CUser extends CModel
    {
        var $validatefunc = array(
                'name' => 'notempty',
                'email' => 'email',
                'age' => array(
                        'notempty',
                        'number'
                    )
            );
    }
    ?>

キーにフィールド名、値にバリデータ関数を指定します。

こうすることによって ``validate`` 関数で自動的にそれぞれのバリデータ関数が呼ばれて検証を行います。
バリデータ関数はフレームワークの ``validate.php`` 内の引数が ``($data, $errmsg = '')`` になっているものです。
必要であれば同じ形式で自由に追加して下さい。

バリデータ関数を配列で指定すると、複数のバリデートを順に行うことが出来ます。

次にモデルのバリデート関数を実行します。 上記のモデルを使用した簡単なチュートリアルを記しますので参考にご覧下さい。

**regist.html**

.. code-block:: php

    <form method="post" action="regist.php">
    名前<br>
    <input type="text" name="user/name"><br>
    メールアドレス<br>
    <input type="text" name="user/email"><br>
    年齢<br>
    <input type="text" name="user/age"><br>
    </form>

**regist.php**

.. code-block:: php

    <?php
        require_once 'config.php';
        require_once 'cheetan.php';

    function action( &$c )
    {
        if ( count( $_POST ) ) {
            if ( $c->ic_user->validate( $c->data['user'] ) ) {
                //検証成功
                $c->ic_user->insert( $c->data['user'] );
            } else {
                $c->set( 'err', 'invalidte!' );
            }
        }
    }
    ?>

モデルの ``$validatemsg`` を以下のように指定しておくことにより、 ``validatemsg`` 関数で検証＋エラーメッセージも流してくれます。

例えば、 ``name`` だけが引っかかった場合、 ``'名前が入力されていません。<br>'`` という文字列が返ります。
また、 ``name`` と ``age`` が引っかかった場合は ``'名前が入力されていません。<br>年齢を入力して下さい。<br>'`` という文字列が返ります。

**user.php**

.. code-block:: php

    <?php
    class CUser extends CModel
    {
        var $validatefunc = array(
                'name' => 'notempty',
                'email' => 'email',
                'age' => array('notempty','number') );

        var $validatemsg  = array(
                'name' => '名前が入力されていません。<br>',
                'email' => 'メールアドレスが正しくありません。<br>',
                'age' => array(
                        '年齢を入力して下さい。<br>',
                        '年齢には数字を入れて下さい。<br>'
                    )
            );
    ?>


**regist.php**

.. code-block:: php

    <?php
        require_once 'config.php';
        require_once 'cheetan.php';


    function action( &$c )
    {
        if ( count( $_POST ) ) {
            $err = $c->ic_user->validatemsg( $c->data['user'] );
            if ( $err == '' ) {
                //検証成功
                $c->ic_user->insert( $c->data['user'] );
            } else {
                $c->set( 'err', $err );
            }
        }
    }
    ?>


ちなみに ``validate`` 関数を使用しても、 ``$validatemsg`` を設定しておけば ``GetValidateError`` 関数でエラーメッセージを配列で取得できます。

.. code-block:: php

    <?php
    $c->ic_user->validate( $c->data['user'] );
    $err = $c->ic_user->GetValidateError();
    $c->set( 'nameerror', $err['name'] );


データを個々に検証
==================

コントローラにバリデータが装備されているので、そのバリデータ関数を直接呼びます。 バリデータは以下のどちらかの変数として利用します。

.. code-block:: php

    <?php
    $c->validate->function();
    $c->v->function();

バリデート関数は ``notempty( $data, $errmsg = '')`` のように引数に任意にエラーメッセージを設定できるようになっており、エラーメッセージが設定されていない場合は検証に成功するとTRUE、失敗するとFALSEが返ります。
エラーメッセージが設定されている場合、検証に成功するとNULL、失敗するとエラーメッセージが返るようになっています。

**エラーメッセージを設定しない時**

.. code-block:: php

    <?php
    if ( $c->v->notempty( $name ) ) {
        $c->set( 'msg', 'OK!' );
    } else {
        $c->set( 'msg', 'Please input your name.' );
    }


**エラーメッセージ設定時**

.. code-block:: php

    <?php
    $err = '';
    $err .= $c->v->notempty( $name, 'Please input your name.' );
    $err .= $c->v->email( $email, 'Please input right email.' );
    if ( $err == '' ) {
        Regist( $name, $email )
    }
    $c->set( $err );


関数
====

* mixed notempty( mixed data [, string errmsg ] )
* mixed len( string data, int min, int max [, string errmsg ] )
* mixed number( mixed data [, string errmsg ] )
* mixed eisu( string data [, string errmsg ] )
* mixed email( string data [, string errmsg ] )

**mixed notempty( mixed data [, string errmsg ] )**

``data`` が ``empty`` でないかを検証します。

**mixed len( string data, int min, int max [, string errmsg ] )**

文字列 ``data`` の長さが ``min`` 以上、 ``max`` 以下であるかを検証します。( ``validatefunc`` には指定できません。）

**mixed number( mixed data [, string errmsg ] )**

``data`` が数字であるかを検証します。

**mixed eisu( string data [, string errmsg ] )**

``data`` が英数字のみで形成されているかを検証します。

**mixed email( string data [, string errmsg ] )**

``data`` がメールアドレスの形式になっているかを検証します。