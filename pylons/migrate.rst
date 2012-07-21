.. Migrating an Existing Pylons Application

既存の Pylons アプリケーションの移行
++++++++++++++++++++++++++++++++++++++++

.. There are two general ways to port a Pylons application to Pyramid. One is to
.. start from scratch, expressing the application's behavior in Pyramid. Many
.. aspects such as the models, templates, and static files can be used unchanged
.. or mostly unchanged. Other aspects like such as the controllers and globals
.. will have to be rewritten. The route map can be ported to the new syntax, or
.. you can take the opportunity to restructure your routes.

Pylons アプリケーションを Pyramid に移植するために、 2 つの一般的な方法
があります。1 つはゼロからスタートして、アプリケーションの振る舞いを
Pyramid で表現することです。モデル、テンプレートおよび静的ファイルなどの
多くの側面はまったく変更せずに、またはほとんど変更せずに使用することが
できます。コントローラやグローバル変数などの他の側面は書き直さなければ
ならないでしょう。 route マップは新しい構文に移植できます。あるいは、
route を再構成する機会ととらえることもできます。


.. The other way is to port one URL at a time, and let Pyramid serve the ported
.. URLs and Pylons serve the unported URLs. There are several ways to do this:

別の方法は、一度に 1 つの URL を移植することです。移植された URL を
Pyramid に処理させ、移植されていない URL を Pylons に処理させます。
これを行う方法がいくつかあります:


.. * Run both the Pyramid and Python applications in Apache, and use mod_rewrite
..   to send different URLs to different applications.
.. * Set up ``paste.cascade`` in the INI file, so that it will first try one
..   application and then the other if the URL returns "Not Found". (This is how
..   Pylons serves static files.)
.. * Wrap the Pylons application in a Pyramid view. See pyramid.wsgiapp.wsgiapp2_.

* Apache の中で Pyramid と Python の両方のアプリケーションを起動して、
  異なる URL を異なるアプリケーションに送るために mod_rewrite を使用する。
* INIファイルの中で ``paste.cascade`` を設定して、その結果、最初に 1 つの
  アプリケーションを試みて、その URL に対して "Not Found" が返る場合に
  もう 1 つのアプリケーションを試みるようにする (これは Pylons が静的
  ファイルを返す方法です) 。
* Pyramid ビューで Pylons アプリケーションをラップする。
  pyramid.wsgiapp.wsgiapp2_ を参照してください。


.. Also see the `Porting Applications to Pyramid`_ section in the Cookbook.

Cookbook の `Porting Applications to Pyramid`_ セクションも参照してください。


.. *Caution:* running a Pyramid and a Pylons application simultaneously may bring up
.. some tricky issues such as coordiating database connections, sessions, data
.. files, etc. These are beyond the scope of this Guide.

*注意:* Pyramid と Pylons アプリケーションを同時に実行することで、
データベース接続や、セッション、データファイルその他の調整といった
いくつかの微妙な問題が表面化するかもしれません。
それらはこのガイドの範囲外です。


.. You'll also have to choose whether to write the Pyramid application in Python 2
.. or 3. Pyramid 1.3 runs on Python 3, along with Mako and SQLAlchemy, and the
.. Waitress and CherryPy HTTP servers (but not PasteHTTPServer). But not all
.. optional libraries have been ported yet, and your application may depend on
.. libraries which haven't been.

さらに、あなたは Pyramid アプリケーションを Python 2 と 3 のどちらで
書くか選ばなければなりません。Pyramid 1.3 は Python 3 で動きます。 Mako
と SQLAlchemy に加えて Waitress および CherryPy HTTPサーバーも同様です
(しかし PasteHTTPServer はそうではありません)。しかし、すべてのオプションの
ライブラリはまだ移植されているとは限らず、あなたのアプリケーションは
Python 3 に移植されていないライブラリに依存するかもしれません。


.. include:: ../links.rst


.. _pyramid.wsgiapp.wsgiapp2: http://docs.pylonsproject.org/projects/pyramid/en/latest/api/wsgi.html#pyramid.wsgi.wsgiapp2
.. _Porting Applications to Pyramid: ../porting/index.html
