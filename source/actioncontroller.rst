======================
アクションコントローラ
======================

処理をひとつひとつのファイルアクセスでなく、CakePHP風にコントローラー内のアクションメソッドとして、まとめて記述する仕様にちいたんを拡張したサンプルです。
サンプルのブログを流用しておりますので違いを見比べてみて頂けると良いと思います。

とりあえず初期設定

* 必要であれば ``.htaccess`` に ``RewriteBase`` を追加
* ``lib/config/database.php`` を修正

上記設定すれば既存のブログサンプルが入っている環境であればそのまま動くと思います。

ファイル構造としては、全て ``lib`` ディレクトリ内に納めています。
ディレクトリを見れば何が入っているかは分かると思います。

``app_controller.php`` は全てのコントローラから継承される基本のコントローラーです。 こちらを利用して処理の共通化なども可能です。

``controllers`` ディレクトリにコントローラを入れておきます。

**blog_data_controller.php**

.. code-block:: php

    <?php
    class CBlog_dataController extends CAppController {
        function index() {

        }
        function edit($id = null) {
            $this->blog_data->findone(array('id' => $id));
        }
    }


| 例えば
| ``/blog_data/index``
| にアクセスすると、
| ``controllers/blog_data_controller.php``
| が読み込まれ、その中の
| ``CBlog_dataController``
| が読み出され、 ``index`` メソッドが呼ばれます。

| 記述すべき処理は今までと同じです。
| しかしコントローラー内の記述となるので、今まで
| ``$c``
| と記述していた箇所は
| ``$this``
| と直接コントローラーメソッドを呼び出す形に変更となります。

| 例えば
| ``/blog_data/edit/112``
| にアクセスすると、
| ``edit`` メソッドが呼ばれますが、
| 引数として ``112`` が指定されます。
| 上の例だと ``$id`` に ``112`` がはいる形になります。



htmlコンポーネント
==================

.. code-block:: php

    <?php echo $c->html->url('/blog_data/index'); ?>


| 今回のURL形式ですと相対URLなどを記述するのが難しいため、
| ``htmlコンポーネント`` を作成致しました。
| 上記の形で指定したURLに飛ぶことが出来ます。
| 詳細はテンプレートをご覧下さい。

| テンプレートは、 ``views/blog_data`` にblog_dataコントローラのテンプレートが入ります。
| 共通テンプレートは ``views/layouts/default.html`` になります。

| mod_rewriteでURL書き換えを行っています。
| 例えばtestという公開ディレクトリに ``.htaccess`` と ``index.php`` がある場合、
| ブラウザ上では
| ``test/blog_data/index``
| といった形で閲覧できます。

| 上記のようにしたくない場合は ``.htaccess`` を削除し、
| ``index.php?url=blog_data/index``
| みたいな感じで閲覧できると思います。


.htaccessのサンプル
===================

.. code-block::

    <IfModule mod_rewrite.c>
        RewriteEngine On
        RewriteBase /actioncontroller/
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteRule ^(.*)$ index.php?url=$1 [QSA,L]
    </IfModule>