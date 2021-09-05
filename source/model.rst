======
モデル
======

データベースのテーブルをモデルによって簡単に操作します。

モデルの作成
============

以下が最小構成のモデルです。 ``テーブル名.php`` で作成し、テーブル名の一文字目を大文字にして以下を小文字にし、 ``C`` をつけたものをクラス名とします。

**user.php**

.. code-block:: php

    <?php
    class CUser extends CModel
    {
    }
    ?>


モデルの登録
============

フレームワークから呼ばれる ``config_models`` 関数内でモデルを登録します。

**config.php**

.. code-block:: php

    <?php
    function config_database( &$db )
    {
        $db->add( '', 'localhost', 'user', 'password', 'dbname' );
        $db->add( 'special', '192.168.0.100', 'user', 'password', 'dbname' );
        $db->add( 'pg', 'localhost', 'user', 'password', 'dbname', DBKIND_PGSQL, '5432' );
    }

    function config_models( &$controller )
    {
        $controller->AddModel( 'user.php' );
        $controller->AddModel( 'specialuser.php', 'special' );
        $controller->AddModel( 'test.php', 'pg' );
    }


使用方法
========

モデルはコントローラにて呼び出します。 呼び出し方は以下の二つの方法があります。 上の方法はコントローラのメンバ変数と競合しない場合のみ設定されています。

.. code-block:: php

    <?php
    $c->user->function();
    $c->m['user']->function();


引数に使用するcondition
=======================

いくつかのメソッドの引数に ``condition`` がありますが、文字列によるSQLの記述の他に変数による指定も出来ます。 今のところ、結合は全てANDです。 
これを使った場合、 ``value`` は自動的にエスケープされます。 

例えば以下の例はどちらも同じ機能になります。

.. code-block:: php

    <?php
    $c->user->find( 'id=1' );
    $c->user->find( array( 'id' => 1 ) );


関数
====

* array find( [ mixed condition [, string order [, string limit [, string group]]] )
* array findby( string field, string value [, string order [, string limit ]] )
* array findone( [ string condition [, string order ]] )
* array findoneby( string field, string value [, string order ] )
* array findquery( string query [, string condition [, string order [, string limit [, string group ]]]] )
* int getcount( [ string condition [, string limit ]] )
* bool insert( array datas )
* bool update( array datas )
* bool updateby( array datas, string condition )
* bool del( string condition )
* result query( string query )
* int GetLastInsertId()
* int GetAffectedRows()
* string GetLastError()
* string to_datetime( [ int time ] )
* string escape( string str )
* bool validate( array datas )
* string validatemsg( array datas )
* array GetValidateError()
* array describe()

**array find( [ mixed condition [, string order [, string limit [, string group ]]] )**

テーブルの中から ``condition`` と ``order`` 、 ``limit`` 、 ``group`` で指定されたものを配列として取得します。

.. code-block:: php

    <?php
    $results = $c->user->find( 'id='.$id, 'age DESC' );

**array findby( string field, string value [, string order [, string limit ]] )**

テーブルから ``field`` と ``value`` 、 ``order`` 、 ``limit`` で指定されたものを配列として取得します。

.. code-block:: php

    <?php
    $results = $c->user->findby( 'user', 'name', 'id ASC' );


**array findone( [ string condition [, string order ]] )**

テーブルの中から ``condition`` と ``order`` で指定されたものの先頭の一つを取得します。

.. code-block:: php

    <?php
    $result = $c->user->findone( 'id='.$id, 'age DESC' );


**array findoneby( string field, string value [, string order ] )**

テーブルの中から ``field`` と ``value`` 、 ``order`` で指定されたものの先頭の一つを取得します。

.. code-block:: php

    <?php
    $result = $c->user->findone( 'id='.$id, 'age DESC' );

**array findquery( string query [, string condition [, string order [, string limit [, string group ]]]] )**

リレーションを使用したい場合はこちらを利用します。 適合したレコードを配列にして取得します。 ``query`` にはWHERE句より前のクエリ文を指定します。

.. code-block:: php

    <?php
    $query = 'SELECT user.*, office.name FROM user LEFT JOIN user.office_id=office.id';
    $results = $c->user->findquery( $query, 'age=24', 'age DESC' );


**int getcount( [ string condition [, string limit ]] )**

テーブルの中の ``condition`` と ``limit`` で当てはまる要素の数を取得します。

.. code-block:: php

    <?php
    $count = $c->user->getcount( 'id='.$id, '10' );

**bool insert( array datas )**

キーに要素名を指定して値を入れた配列を指定すると、その通りにINSERTを行います。

.. code-block:: php

    <?php
    $data['name'] = $name;
    $data['email'] = $email;
    $c->user->insert( $data );

**bool update( array datas )**

キーに要素名を指定して値を入れた配列を指定すると、その通りにUPDATEを行います。
モデルのメンバ変数 ``$id`` （デフォルト:  ``'id'`` ）と同じキーを見つけ、自動的にその場所をUPDATEします。
``$id`` が見つからなかった場合は ``FALSE`` を返し処理を中止します。

.. code-block:: php

    <?php
    $data['id'] = $id;
    $data['name'] = $name;
    $data['email'] = $email;
    $c->user->update( $data );

**bool updateby( array datas, string condition )**

updateの様に自動的に条件を指定するのではなく、明示的に ``condition`` で条件を指定できます。

.. code-block:: php

    <?php
    $data['name'] = $name;
    $data['email'] = $email;
    $c->user->updateby( $data, 'age=25' );

**bool del( string condition )**

``condition`` で指定されたレコードを削除します。

.. code-block:: php

    <?php
    $c->user->del( 'age=25' );

**result query( string query )**

直接クエリを送信します。 テーブル名は自動的には設定されませんので完全なクエリを指定します。

.. code-block:: php

    <?php
    $results = $c->user->query( "SELECT * FROM user WHERE age='24' ORDER BY age DESC" );

**int GetLastInsertId()**

最後に挿入したIDを返します。（MySQLのみ）

.. code-block:: php

    <?php
    $lastId = $c->user->GetLastInsertId();

**int GetAffectedRows()**

SQLで影響を受けた行の数を返します。

.. code-block:: php

    <?php
    $count = $c->user->GetAffectedRows();

**string GetLastError()**

最後に得たエラーを返します。

.. code-block:: php

    <?php
    $error = $c->user->GetLastError();

**string to_datetime( [ int time ] )**

指定されたUNIXタイムスタンプをDATETIMEの様式にフォーマットします。 （例: 2006-09-19 12:24:46）

.. code-block:: php

    <?php
    $date = $c->user->to_datetime();

**string escape( string str )**

文字列をクエリ用にエスケープします。

.. code-block:: php

    <?php
    $query = $c->user->escape( $query );

**bool validate( array datas )**

**string validatemsg( array datas )**

**array GetValidateError()**

これらはバリデート用関数です。 詳しくはバリデートのマニュアルをご覧下さい。

**array describe()**

テーブルのカラム情報を得ます。

.. code-block:: php

    <?php
    $columns = $c->user->describe();

