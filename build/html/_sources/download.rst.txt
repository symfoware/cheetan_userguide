==============
リリースノート
==============

* Version 0.8.1.0 - 2009-09-14

::

    model.phpのquery関数修正
    modelにdescribe関数追加


* Version 0.8.0.8 - 2008-02-23

::

    MySQLにポート指定追加bugfix
    validateを複数指定できるように仕様拡張


* Version 0.7.9.9 - 2008-02-20

::

    PgSQLのバグfix
    MySQLにポート指定追加
    modelに一部自動エスケープを追加
    condition指定に配列仕様追加
    view上の変数参照を拡張（$data['var'] から $var でアクセス出来るように）


* Version 0.7.8.2 - 2007-07-06

::

    PgSQLのバグfix
    PgSQLにポート指定追加
    $db->add( 'pg', 'host', 'user', 'password', 'dbname', DBKIND_PGSQL, 'port' );


* Version 0.7.5.6 - 2007-06-01

::

    SQLロギングを追加
    複数データベース利用時処理のバグ修正
    モデルにSQL実行情報取得関数を追加

* Version 0.7.1.9 - 2007-05-29

::

    DB周りを変更。(無駄な接続の排除、escape関数）
    DISPATCHの実行順序変更

* Version 0.7.0.9 - 2007-05-13

::

    主にバグ修正。
    pgsqlのfind関数のバグ
    notemptyの修正
    textsqlロックし忘れ
    textsqlに「start,limit」形式指定追加
    メールアドレスのバリデート

* Version 0.7.0.1 - 2006-11-04

::

    コンフィグ関数にconfig_view_class関数を追加
    コントローラにSetViewPath関数を追加
    不必要なsql操作クラス(db以下)を削除しても良いように修正

* Version 0.6.1.2 - 2006-10-20

::

    コンフィグ関数にconfig_controller_class関数を追加

* Version 0.5.9.8 - 2006-10-14

::

    コンポーネントの概念を追加
    コンフィグ関数にafter_action関数を追加

* Version 0.5.7.7 - 2006-10-7

::

    TextSQLに対応
    データベース周りの修正など

* Version 0.0.0.7 - 2006-9-26

::

    PostgreSQLに対応

* Version 0.0.0.3 - 2006-9-20

::

    バリデータemail関数のバグ修正
    モデルにfindby,findoneby関数を追加

* Version 0.0.0.2 - 2006-9-18

::

    モデルのバグ修正($order->$limit)

* Version 0.0.0.1 - 2006-9-10

::

    初回リリース