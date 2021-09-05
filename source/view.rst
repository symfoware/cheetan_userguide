======
ビュー
======

実際に表示するHTMLの記述にはいくつかの方法があります。 状況に応じて好きなものを選択することが可能です。

* PHPファイルに直接書く
* smartyの用にPHPファイルとテンプレートを分ける（構文解析が無い分恐らく高速）
* 全ファイル共通のテンプレートを用意し、コンテンツ部分をPHPファイルに直接書く
* 全ファイル共通のテンプレートを用意し、PHPファイルとテンプレートを分ける


PHPファイルに直接書く
=====================

小さな個人用プログラムの場合はこちらが一番楽でしょう。

**test.php**

.. code-block:: php

    <?php
        require_once 'cheetan.php';

    function action( &$c )
    {
        $c->set( 'msg', 'Hello' );
    }
    ?>
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
    <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="ja" lang="ja">
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>テスト</title>
    </head>
    <body>
    <h1><?php echo $data['msg'];?></h1>
    </body>
    </html>


PHPファイルとテンプレートを分ける
=================================

smartyのようにPHPのアクション部分と表示部分を分割します。 構文解析は無いので速いです。

**test.php**

.. code-block:: php

    <?php
        require_once 'cheetan.php';

    function action( &$c )
    {
        $c->set( 'msg', 'Hello' );
    }
    ?>


**test.html**

.. code-block:: php

    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
    <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="ja" lang="ja">
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>テスト</title>
    </head>
    <body>
    <h1><?php echo $data['msg'];?></h1>
    <!-- この形式の場合以下も可能 -->
    <h2><?php echo $msg;?></h2>
    </body>
    </html>

何も設定を行わなければ同じフォルダ内の拡張子を ``.html`` に変更したものが使用されます。
違うものを指定したい場合はコントローラの ``SetViewFile(string filename)`` を使用して下さい。


全ファイル共通のテンプレート + PHPファイルに直接書く
====================================================

全てのページに共通のテンプレートファイルを使用することが出来ます。
これにより各々のページにはコンテンツ部分のみを記述すればよいので作業が簡略化します。

テンプレートファイルを指定するには、コントローラもしくはコンフィグ関数の中から ``SetTemplateFile(string filename)`` により指定して下さい。

**template.html**

.. code-block:: php

    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
    <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="ja" lang="ja">
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>テスト</title>
    </head>
    <body>
    <?php contents( $data );?>
    <!-- 共通テンプレートを使用する場合以下の形でsetした変数参照可能 -->
    <?php echo $msg;?>
    </body>
    </html>


**test.php**

.. code-block:: php

    <?php
        require_once 'cheetan.php';

    function action( &$c )
    {
        $c->set( 'msg', 'Hello' );
    }

    function contents( $data )
    {
    ?>
    <h1><?php echo $data{'msg'];?></h1>
    <?php
    }
    ?>


全ファイル共通のテンプレート+PHPファイルとテンプレートを分ける
==============================================================

全てのページに共通のテンプレートファイルを使用し、なおかつアクション部分と表示部分を分けることが出来ます。
上記と ``template.html`` の書き方が微妙に違うので注意して下さい。

**template.html**

.. code-block:: php

    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
    <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="ja" lang="ja">
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>テスト</title>
    </head>
    <body>
    <?php $this->content( $data );?>
    <!-- 共通テンプレートを使用する場合以下の形でsetした変数参照可能 -->
    <?php echo $msg;?>
    </body>
    </html>


**test.php**

.. code-block:: php

    <?php
        require_once 'cheetan.php';

    function action( &$c )
    {
        $c->set( 'msg', 'Hello' );
    }
    ?>


**test.html**

.. code-block:: php

    <h1><?php echo $data['msg'];?></h1><br /><!-- この形式の場合以下も可能 -->
    <h2><?php echo $msg;?></h2>
