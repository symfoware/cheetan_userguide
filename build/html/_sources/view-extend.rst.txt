======================
ビューのオーバーライド
======================

ビュークラスをオーバーライドしたい時に使用します。 次のように宣言します。

.. code-block:: php

    <?php
    function config_view_class()
    {
        class CMyView extends CView
        {

            :
            :
            :

        }
        return 'CMyView';
    }


機能拡張: 例
============

ロギング
--------

SQLログをXHTMLに準拠したテーブル表示に変更します。

.. code-block:: php

    <?php
    class CMyView extends CView
    {
        function SetSqlLog( $sqllog )
        {
            if( $this->debug )
            {
                $log	= '<table class="cheetan_sql_log">' . "\n"
                        . '<tr>'
                        . '<th style="width:60%;">SQL</th>'
                        . '<th style="width:10%;">ERROR</th>'
                        . '<th style="width:10%;">ROWS</th>'
                        . '<th style="width:10%;">TIME</th>'
                        . '</tr>' . "\n";
                foreach( $sqllog as $name => $rows )
                {
                    $log	.= '<tr><td colspan="4"><strong>' . htmlspecialchars( $name, ENT_QUOTES, SYS_ENCODE ) . '</strong></td></tr>';
                    foreach( $rows as $i => $row )
                    {
                        $log	.= '<tr>'
                                . '<td style="text-align:left;">' . htmlspecialchars( $row['query'], ENT_QUOTES, SYS_ENCODE ) . '</td>'
                                . '<td style="text-align:left;">' . htmlspecialchars( $row['error'], ENT_QUOTES, SYS_ENCODE ) . '</td>'
                                . '<td style="text-align:center;">' . $row['affected_rows'] . '</td>'
                                . '<td style="text-align:center;">' . sprintf( '%.5f', $row['query_time'] ) . '</td>'
                                . '</tr>' . "\n";
                    }
                }
                $log	.= '</table>' . "\n";
                $this->variables['cheetan_sql_log'] = $log;
            }
        }
    }