==============
チュートリアル
==============

ブログを作成する例を見ながら、以下を参考にちいたんフレームワークの何を、どのように使用するかを決定しましょう。

データベースを使う
==================

現在ちいたんフレームワークはMySQL, PostgreSQL, TextSQLに対応しています（その他にも柔軟に対応可能）。 

チュートリアルではMySQLでの説明をします。 
データベースの初期化は、フレームワークによって呼ばれる ``config_database`` 関数によって設定します。 以下に例を示します。

どのファイルからも参照できるように ``config.php`` というコンフィグファイルを作成してそこに関数を書きます。

**config.php**

.. code-block:: php

    <?php
    function config_database( &$db )
    {
        $db->add( '', 'localhost', 'user', 'password', 'dbname' );
    }


モデルを使う
============

モデルはテーブルを扱うクラスです。これを用意するだけでソースを書く必要が無くなります。 試してみましょう。

まずブログ用に以下の様なテーブルを作成します。

.. code-block:: sql

    CREATE TABLE `blog_data` (
    `id` int(11) NOT NULL auto_increment,
    `title` text NOT NULL,
    `body` text NOT NULL,
    `modified` timestamp NOT NULL default CURRENT_TIMESTAMP on update CURRENT_TIMESTAMP,
    PRIMARY KEY  (`id`)
    );

次にモデルを作成します。 判りやすいようにmodelsフォルダ内にでも入れておきましょう。

**models/blog_data.php**

.. code-block:: php

    <?php
    class CBlog_data extends CModel
    {
    }

あれ、何も書いてない…そうなんです。 これだけでデータベースの読み書きが出来てしまいます。 どのように行うかは後のお楽しみ。 びっくりするほど簡単です。

では、このモデルを使用するために、 ``config.php`` 内にフレームワークから呼ばれる関数を追加しましょう。

**config.php追加分**

.. code-block:: php

    function config_models( &$controller )
    {
        $controller->AddModel( dirname( __FILE__ ) . '/models/blog_data.php' );
    }



ビューを使う
============

ビューは表示部分です。実際のHTMLを書きます。 HTMLを書くにはなんとこれだけの方法が用意されています。

* PHPファイルに直接書く
* smartyの用にPHPファイルとテンプレートを分ける（構文解析が無い分恐らく高速）
* 全ファイル共通のテンプレートを用意し、コンテンツ部分をPHPファイルに直接書く
* 全ファイル共通のテンプレートを用意し、PHPファイルとテンプレートを分ける

詳しくはマニュアルのビューにて解説しています。 今回は４番目の全ファイル共通のテンプレートを用意し、PHPファイルとテンプレートを分ける方法を使用します。

まず全ファイル共通テンプレートを用意しましょう。

**template.html**

.. code-block:: php

    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
    <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="ja" lang="ja">
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>ちいたんブログ</title>
    <link href="style.css" rel="stylesheet" type="text/css" />
    </head>
    <body>
    <?php echo $this->content();?>
    </body>
    </html>

とまあ、こんなものでしょうか。 今回の場合 ``$this->content();`` のところに各ページのコンテンツが表示されます。

続いてブログを書き込むページを用意します。 共通テンプレートを使用しているので中身だけです。

**add.html**

.. code-block:: php

    <form method="post" action="add.php">
    タイトル<br />
    <input type="text" name="blog/title" /><br />
    内容<br />
    <textarea cols="40" rows="8" name="blog/body"></textarea><br />
    <input type="submit" value="書き込み" />
    </form>


コントローラーを使う
====================

コントローラーにより今まで作成したモデル、ビュー全てを連結し、動作させます。 ファイルは何の指定もなければビューの拡張子を.phpに変えたものです。

まずは先ほどビューで作成したフォームにより、データを保存するプログラムがどれだけ少ないかご覧下さい。

**add.php**

.. code-block:: php

    <?php
        require_once 'config.php';
        require_once 'cheetan/cheetan.php';

    function action( &$c )
    {
        $c->SetTemplateFile( 'template.html' );
        if ( count( $_POST ) ) {
            $c->blog_data->insert( $c->data['blog'] );
        }
    }

blog_dataというモデルがここで使用されているのがおわかりでしょうか。 ビューでインプットの名前に ``blog/title`` などという変な名前を指定していたのは、 ``blog/`` で指定されているデータをコントローラ側で ``$data['blog']`` 配列に自動的に挿入するためでした。

こうやって必要なデータだけを配列に集め、insertします。


一覧表示
========

では書き込んだデータを一覧表示してみましょう。

**view.php**

.. code-block:: php

    <?php
        require_once 'config.php';
        require_once 'cheetan/cheetan.php';

    function action( &$c )
    {
        $c->SetTemplateFile( 'template.html' );
        $c->set( 'datas', $c->blog_data->find() );
    }


**view.html**

.. code-block:: php

    <?php foreach( $data['datas'] as $data ){ ?>
    <table>
    <tr>
    <td><?php echo $data['title'];?></td>
    <td><?php echo str_replace( "¥n", '<br />', $data['body'] );?></td>
    <td><?php echo $data['modified'];?></td>
    </tr>
    </table>
    <?php } ?>

このように ``set`` 関数によりビュー内で値を ``$data`` として参照することが出来ます。


データの編集
============

ではデータを編集してみましょう。

**edit.php**

.. code-block:: php

    <?php
        require_once 'config.php';
        require_once 'cheetan/cheetan.php';

    function action( &$c )
    {
        $c->SetTemplateFile( 'template.html' );
        if ( count( $_POST ) ) {
            $c->blog_data->update( $c->data['blog'] );
        }
        $c->set( 'data', $c->blog_data->findone( 'id=' . $_GET['id'] ) );
    }

**edit.html**

.. code-block:: php

    <form method="post" action="edit.php">
    タイトル<br />
    <input type="text" name="blog/title" value="<?php echo $data['data']['title'];?>" /><br />
    内容<br />
    <textarea cols="40" rows="6" name="blog/body"><?php echo $data['data']['body'];?></textarea><br />
    <input type="hidden" name="blog/id" value="<?php echo $data['data']['id'];?>" />
    <input type="submit" value="更新" />
    </form>


データの削除
============

ではデータを削除してみましょう。 もうある程度予想がつきますね。

**del.php**

.. code-block:: php

    <?php
        require_once 'config.php';
        require_once 'cheetan/cheetan.php';

    function action( &$c )
    {
        $c->SetTemplateFile( 'template.html' );
        if ( count( $_POST ) ) {
            $c->blog_data->del( 'id=' . $_POST['id'] );
        }
        $c->set( 'data', $c->blog_data->findone( 'id=' . $_GET['id'] ) );
    }


**del.html**

.. code-block:: php

    <form method="post" action="del.php">
    削除してもよろしいですか？<br />
    タイトル<br />
    <?php echo $data['data']['title'];?><br />
    内容<br />
    <?php echo $data['data']['body'];?><br />
    <input type="hidden" name="id" value="<?php echo $data['data']['id'];?>" />
    <input type="submit" value="削除" />
    </form>


まとめ
======

これで一通りブログっぽくなりました。このように非常に簡単にアプリケーションを作成することが出来ます。 もうフレームワークを使わないプログラムやモデルのないフレームワークなんて使う気になれませんよね？

ちいたんフレームワークにはもうちょっと便利な機能が用意されています。 是非マニュアルを参考に色々と試してみてください。