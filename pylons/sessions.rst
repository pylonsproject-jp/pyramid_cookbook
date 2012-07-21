.. Sessions

セッション
++++++++++

.. Pyramid uses Beaker sessions just like Pylons, but they're not enabled by
.. default. To use them you'll have to add the "pyramid_beaker" package as a
.. dependency, and put the following line in your ``main()`` function:

Pyramid は Pylons と同じように Beaker セッションを使用しますが、それは
デフォルトで有効になっていません。 Beaker セッションを使用するためには、
依存関係として "pyramid_beaker" パッケージを加えて、 ``main()`` 関数に
次の行を入れてください。


::

    config.include("pyramid_beaker")


.. (To add a dependency, put it in the ``requires`` list in setup.py, and
.. reinstall the application.)

(依存関係を追加するために、 setup.py の中の ``requires`` リストにそれを
入れて、アプリケーションを再インストールしてください)


.. The default configuration is in-memory sessions and (I think) no caching. You
.. can customize this by putting configuration settings in your INI file or in the
.. ``settings`` dict at the beginning of the ``main()`` function (before the
.. Configurator is instantiated).  The Akhet Demo configures Beaker with the
.. following settings, borrowed from the Pylons configuration:

デフォルトの設定は in-memory セッションで、キャッシュはありません
(と思います) 。INI ファイル、あるいは ``main()`` 関数の最初の ``settings``
辞書に (Configurator がインスタンス化される前に) 設定を含めることで、
これをカスタマイズすることができます。 Akhet デモでは、Pylons 設定から
拝借した次の設定値で Beaker を設定します:


.. code-block:: ini

    # Beaker cache
    cache.regions = default_term, second, short_term, long_term
    cache.type = memory
    cache.second.expire = 1
    cache.short_term.expire = 60
    cache.default_term.expire = 300
    cache.long_term.expire = 3600

    # Beaker sessions
    #session.type = file
    #session.data_dir = %(here)s/data/sessions/data
    #session.lock_dir = %(here)s/data/sessions/lock
    session.type = memory
    session.key = akhet_demo
    session.secret = 0cb243f53ad865a0f70099c0414ffe9cfcfe03ac


.. To use file-based sessions like in Pylons, uncomment the first three session
.. settings and comment out the "session.type = memory" line.

Pylons のようにファイルベースのセッションを使用するためには、最初の3行
のセッション設定のコメントを外して、 "session.type = memory" 行を
コメントアウトしてください。


.. You should set the "session.secret=" setting to a random string. It's used to
.. digitally sign the session cookie to prevent session hijacking.

"session.secret=" 設定をランダムな文字列に設定すべきです。それは
セッションハイジャックを防ぐためのセッション Cookie にディジタル署名
するために使用されます。


.. Beaker has several persistence backends available, including memory, files,
.. SQLAlchemy, memcached, and cookies (which stores each session variable in a
.. client-side cookie, and has size limitationss). The most popular
.. deployment backend nowadays is memcached, which can act as a shared storage
.. between several processes and servers, thus providing the speed of memory with
.. the ability to scale to a multi-server cluster. Pylons defaults to disk-based
.. sessions.

Beaker は、いくつかの永続化バックエンドが利用可能です。メモリ、ファイル、
SQLAlchemy 、 memcached および Cookie (各セッション変数をクライアント側
Cookie に格納し、サイズ制限がある) が含まれます。最近最も人気のある
デプロイバックエンドは memcached です。それは複数のプロセスとサーバの間
の共有ストレージとして動作して、メモリの速度と複数サーバクラスタにスケール
する能力を提供します。Pylons はディスクベースのセッションをデフォルトに
しています。


.. Beaker plugs into Pyramid's built-in session interface, which is accessed via
.. ``request.session``.  Use it like a dict. Unlike raw Beaker sessions, you don't
.. have to call ``session.save()`` every time you change something, but you should
.. call ``session.changed()`` if you've modified a *mutable* item in the session;
.. e.g., ``session["mylist"].append(1)``.

Beaker は (``request.session`` でアクセスされる) Pyramid の内蔵セッション・
インタフェースにプラグインされます。それを辞書のように使用してください。
生の Beaker セッションと異なり、何かを変更する度に ``session.save()``
を呼ぶ必要はありません。しかし、もしセッション内で *mutable な* アイテムを
修正した場合は、 ``session.changed()`` を呼ぶ必要があります。


.. The Pyramid session interface also has some extra features. It can store a set
.. of "flash messages" to display on the next page view, which is useful when you
.. want to push a success/failure message and redirect, and the message will be
.. displayed on the target page. It's based on ``webhelpers.flash``, which is
.. incompatible with Pyramid because it depends on Pylons' magic globals. There
.. are also methods to set a secure form token, which prevent form submissions
.. that didn't come from a form requested earlier in the session (and thus may be
.. a cross-site forgery attack).  (Note: flash messages are not related to the Adobe
.. Flash movie player.)

Pyramid セッションインタフェースにはさらにいくつかの追加機能があります。
次のページビューに表示する「フラッシュメッセージ」を格納することができます。
これは、成功/失敗メッセージを格納してリダイレクトしたい場合に有用です。
メッセージは目標ページに表示されるでしょう。この機能は
``webhelpers.flash`` に基づきます。それは Pylons の特殊グローバル変数に
依存するので、 Pyramid と互換性がありません。また、セキュアフォーム・
トークンを設定する方法もあります。それはセッションのそれ以前にリクエスト
されたフォーム以外からの (したがって、クロスサイト・フォージェリ(偽装)
攻撃かもしれない) フォーム送信を防ぎます。 (Note: フラッシュメッセージ
は Adobe Flash ムービープレーヤーとは関係ありません。)


.. See the :ref:`sessions_chapter` chapter in the Pyramid manual for the API of
.. all these features and other features.  The Beaker_ manual will help you
.. configure a backend. The Akhet_ Demo is an example of using Pyramid with
.. Beaker, and has flash messages.

これらのすべての機能および他の機能の API については Pyramid マニュアル
の :ref:`sessions_chapter` 章を参照してください。 Beaker_ マニュアルは
バックエンドを設定する上で役に立つでしょう。 Akhet_ デモは Pyramid で
Beaker を使用する例で、フラッシュメッセージを持っています。


.. *Note:* I sometimes get an exception in the debug toolbar when sessions are
.. enabled.  They may be a code discrepency between the distributions. If this
.. happens to you, you can disable the toolbar until the problem is fixed.

*Note:* 私がセッションを有効にしている間 debug ツールバーの中で時々例外が
起こりました。それはディストリビューション間のコードの不一致によるもの
かもしれません。これがあなたの所でも起きる場合、この問題が解決されるまで
ツールバーを無効にすることができます。



.. include:: ../links.rst
