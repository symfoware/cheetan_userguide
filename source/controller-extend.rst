============================
コントローラのオーバーライド
============================

コントローラクラスをオーバーライドしたい時に使用します。 次のように宣言します。

.. code-block:: php

    <?php
    function config_controller_class()
    {
        class CMyController extends CController
        {

            :
            :
            :

        }
        return 'CMyController';
    }


機能拡張: 例
============

XSSフィルタリング
-----------------

フォーム入力をクロスサイトスクリプティング（XSS）フィルタリングします。
フィルタリングされたフォーム入力は、コントローラクラスの変数 ``$get`` と ``$post`` 、 ``$request`` から取得することが出来ます。

.. code-block:: php

    <?php
    $name = $c->get['name'];


.. important::
    グローバル変数の ``$_GET`` と ``$_POST`` 、 ``$_REQUEST`` はXSSフィルタリングされません。


.. code-block:: php

    <?php
    class CMyController extends CController
    {
        function RequestHandle()
        {
            $get	 = $this->_sanitize( $_GET );
            $post	 = $this->_sanitize( $_POST );
            $request = $this->_sanitize( $_REQUEST );

            if ( count( $get ) )	 $this->get 	= $get;
            if ( count( $post ) )	 $this->post	= $post;
            if ( count( $request ) ) $this->request	= $request;
            $this->ModelItemHandle( $get );
            $this->ModelItemHandle( $post );
        }

        function _sanitize( $request )
        {
            $request = is_array( $request ) ?
                array_map( array( get_class( $this ), '_sanitize'), $request ) :
                htmlentities( $request, ENT_QUOTES, mb_internal_encoding() );

            return $request;
        }
    }


フォーム入力の多次元配列による取得
----------------------------------

ちいたんは標準でフォーム入力を二次元配列で取得します。これを多次元配列に拡張します。

.. code-block:: php

    <?php
    class CMyController extends CController
    {
        function ModelItemHandle( $requests )
        {
            foreach( $requests as $key => $request ) {
                if( strpos( $key, '/' ) !== FALSE ) {
                    $model = explode( '/', $key );
                    $num = count( $model );
                    $str = '$this->data';
                    for ( $loop = 0; $loop < $num; $loop++ ) {
                        $str .= '[$model[' . $loop . ']]';
                    }
                    $str .= "='{$request}';";
                    eval( $str );
                }
            }
        }
    }
