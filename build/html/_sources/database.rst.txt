============
データベース
============

ちいたんで使用可能なデータベースは、標準でMySQL, PostgreSQL, TextSQLの3種類です。 オプション（ソースコードの修正）でSQLiteも使用可能です。


定数
====

* DBKIND_MYSQL --- データベースにMySQLを使用します。
* DBKIND_PGSQL --- データベースにPostgreSQLを使用します。
* DBKIND_TEXTSQL --- データベースにTextSQLを使用します。
* DBKIND_SQLITE --- データベースにSQLite(v2.x.x)を使用します。
* DBKIND_PDO_SQLITE --- データベースにSQLite(v3.x.x)を使用します。
* DBKIND_SQLITE3 --- データベースにSQLite(v3.x.x)を使用します。【PHP 5.3.0以上が必要】

【`SQLite対応差分 <https://symfo.web.fc2.com/etc/cheetan/userguide/cheetan0810_sqlite.diff>`_】


関数
====

* void add( string name, string host, string user, string pswd, string db [, int kind [, int port ]] )

**void add( string name, string host, string user, string pswd, string db [, int kind [, int port ]] )**

データベースを追加します。kindを省略するとMySQLが選択されます。

.. code-block:: php

    <?php
    function config_database( &$db )
    {
        $db->add( '', 'host', 'user', 'pswd', 'db', DBKIND_MYSQL );
    }