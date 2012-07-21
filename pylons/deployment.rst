.. Deployment

デプロイ
++++++++++

.. Deployment is the same for Pyramid as for Pylons. Specify the desired WSGI
.. server in the "[server:main]" and run "pserve" with it. The default server in
.. Pyramid is Waitress, compared to PasteHTTPServer in Pylons. 

デプロイに関しては Pyramid と Pylons で同じです。 "[server:main]" の中で
好きな WSGI サーバを指定して、それと共に "pserve" を実行してください。
Pylons では標準サーバは PasteHTTPServer でしたが、 Pyramid の標準サーバ
は Waitress です。


.. Waitress' advantage is that it runs on Python 3. Its disadvantage is that it
.. doesn't seek and destroy stuck threads like PasteHTTPServer does. If you're
.. like me, that's enough reason not to use Waitress in production. You can switch
.. to PasteHTTPServer or CherryPy server if you wish, or use a method like
.. mod_wsgi that doesn't require a Python HTTP server. 

Waitress の利点は、 Python 3 上で動作するということです。欠点は、
PasteHTTPServer が行うように、スタックしたスレッドを検索して破壊しない
ということです。あなたが私のようであれば、それはプロダクション環境の中
で Waitress を使用しないのに十分な理由です。希望すれば、
PasteHTTPServer や CherryPy サーバに切り替えるか、あるいは Python HTTP
サーバを要求しない mod_wsgi のような方法を使用することができます。
