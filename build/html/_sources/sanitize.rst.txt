==========
サニタイズ
==========

コントローラとビューにはサニタイザが装備されています。
機能が自動化されておらず中途半端のため、とりあえずのマニュアルなので詳しい方法は書きません。

次の方法でサニタイザ( ``CSanitize`` クラス)にアクセスできますので、ライブラリの ``sanitize.php`` 内の関数を参照して下さい。
例は全て ``htmlspecialchars`` を施す場合です。

コントローラで使用する場合
==========================

.. code-block:: php

    <?php
    function action( &$c )
    {
        $email = $c->sanitize->html( $_POST['email'] );
        $email = $c->s->html( $_POST['email'] );
        $email = $c->s->post( 'email' );
    }


ビューで使用する場合
====================

.. code-block:: php

    <?php
    <h3><?php print $sanitize->html( $data['email'] );?></h3>
    <h3><?php print $s->html( $data['email'] );?></h3>
