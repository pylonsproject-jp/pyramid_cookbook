.. Launching the Application

アプリケーションの起動
+++++++++++++++++++++++++

.. Pyramid and Pylons start up identically because they both use PasteDeploy and
.. its INI-format configuration file.  This is true even though Pyramid 1.3
.. replaced "paster serve" with its own "pserve" command. Both "pserve" and
.. "paster serve" do the following:

Pyramid と Pylons は、両方とも PasteDeploy およびその INI フォーマット
設定ファイルを使用するので、同様に開始します。これは、たとえ Pyramid 1.3 が
"paster" を自分自身のコマンドに置き換えたとしても真です。 "pserve" と
"paster serve" の両方は下記を行います:


.. 1. Read the INI file.
.. 2. Instantiate an application based on the "[app:main]" section.
.. 3. Instantiate a server based on the "[server:main]" section.
.. 4. Configure Python logging based on the logging sections.
.. 5. Call the server with the application.

1. INI ファイルを読む。
2. "[app:main]" セクションに基づいてアプリケーションをインスタンス化する。
3. "[server:main]" セクションに基づいてサーバをインスタンス化する。
4. logging セクションに基づいて Python logging を構成する。
5. アプリケーションとともにサーバを呼び出す。


.. Steps 1-3 and 5 are essentially wrappers around PasteDeploy. Only step 2 is
.. really "using Pyramid", because only the application depends on other parts of
.. Pyramid. The rest of the routine is copied directly from "paster serve" and
.. does not depend on other parts of Pyramid.

ステップ 1-3 と 5 は本質的に PasteDeploy のラッパーです。ステップ 2
だけが実際に「Pyramid を使用」しています。なぜならアプリケーションだけが
Pyramid の他の部分に依存するからです。ルーチンの残りは "paster serve"
から直接コピーされ、 Pyramid の他の部分には依存しません。


.. The way the launcher instantiates an application is often misunderstood so
.. let's stop for a moment and detail it. Here's part of the app section in the
.. Akhet Demo:

ランチャーがアプリケーションをインスタンス化する方法はしばしば誤解されます。
したがって、しばらく立ち止まって詳細を説明しましょう。これは Akhet デモの
app セクションの一部です:


.. code-block:: ini

    [app:main]
    use = egg:akhet_demo#main
    pyramid.reload_templates = true
    pyramid.debug_authorization = false


.. The "use=" line indirectly names a Python callable to load. "egg:" says to look up a
.. Python object by entry point. (Entry points are a feature provided by
.. Setuptools, which is why Pyramid/Pylons require it or Distribute to be
.. installed.) "akhet_demo" is the name of the Python
.. distribution to look in (the Pyramid application), and "main" is the entry 
.. point. The launcher calls
.. ``pkg_resources.require("akhet_demo#main")`` in Setuptools, and Setuptools
.. returns the Python object.  Entry points are defined in the distribution's
.. setup.py, and the installer writes them to an entry points file. Here's the
.. *akhet_demo.egg-info/entry_points.txt* file:

"use=" 行は間接的に Python callable をロードするように指名します。
"egg:" は、エントリポイントによって Python オブジェクトを調べるように言います
(エントリポイントは Setuptools によって提供される機能です。これが
Pyramid/Pylons が Distribute のインストールを要求する理由です)。
"akhet_demo" は、 look in する Python ディストリビューション (Pyramid
アプリケーション) の名前です。また "main" はエントリポイントです。
ランチャは Setuptools の ``pkg_resources.require("akhet_demo#main")`` を
呼び、 Setuptools が Python オブジェクトを返します。エントリポイントは
ディストリビューションの setup.py に定義されています。また、インストーラ
はエントリポイント・ファイルにそれらを書きます。
これは *akhet_demo.egg-info/entry_points.txt* ファイルです:


.. code-block:: ini

    [paste.app_factory]
    main = akhet_demo:main


.. "paste.app_factory" is the entry point group, a name publicized in the
.. PasteDeploy docs for all applications that want to be compatible with it.
.. "main" (on the left side of the equal sign) is the entry point.
.. "akhet_demo:main" says to import the ``akhet_demo`` package and load a "main"
.. attribute. This is our ``main()`` function defined in
.. *akhet_demo/\_\_init\_\_.py*. The other options in the "[app:main]" section
.. become keyword arguments to this callable. These options are called "settings"
.. in Pyramid and "config variables" in Pylons. (The options in the "[DEFAULT]"
.. section are also passed as default values.) Both frameworks provide a way to
.. access these variables in application code. In Pyramid they're in the
.. ``request.registry.settings`` dict. In Pylons they're in the ``pylons.config``
.. magic global. 

"paste.app_factory" は、エントリポイント・グループです (互換性を保ちたい
すべてのアプリケーションのためにPasteDeploy ドキュメント中で公表されている
名前)。 "main" (等号の左側) はエントリポイントです。
"akhet_demo:main" は、 ``akhet_demo`` パッケージをインポートして "main"
属性をロードするように言います。これは *akhet_demo/\_\_init\_\_.py* で
定義された ``main()`` 関数です。 "[app:main]" セクションの他のオプション
は、この callable へのキーワード引数になります。これらのオプションは、
Pyramid では "settings" 、Pylons では "config variables" と呼ばれます。
("[DEFAULT]" セクション中のオプションも、デフォルト値として渡されます。)
両方のフレームワークは、アプリケーションコード中でこれらの変数にアクセス
する方法を提供します。 Pyramid では ``request.registry.settings`` 辞書の
中に、Pylons では ``pylons.config`` 特殊グローバル変数の中にあります。


.. The launcher loads the server in the same way, using the "[server:main]"
.. section.

ランチャーは、 "[server:main] セクションを使って同じ方法でサーバーを
ロードします。


.. *More details:* The heavy lifting is done by ``loadapp()`` and ``loadserver()``
.. in ``paste.deploy.loadwsgi``.  Loadwsgi is obtuse and undocumented, but
.. ``pyramid.paster`` has some convenience functions that either call or mimic some of
.. its routines.

*より詳細:* ``paste.deploy.loadwsgi`` の中で ``loadapp()`` および
``loadserver()`` によって重労働 (heavy lifting) が行われます。 loadwsgi
は難解 (obtuse) でドキュメント化されていませんが、 ``pyramid.paster`` には
そのルーチンのいくつかを呼ぶ、または模倣する便利な関数があります。


.. Alternative launchers such as mod_wsgi read only the "[app:main]" section and
.. ignore the server section, but they're still using PasteDeploy or the
.. equivalent. It's also possible to instantiate the application manually without an
.. INI file or PasteDeploy, as we'll see in the chapter called "The Main Function".

mod_wsgi のような代替ランチャは、 "[app:main]" セクションだけを読んで
server セクションを無視しますが、それらもやはり PasteDeploy またはその
等価物を使用しています。 "The Main Function" の章で見るように、 INI
ファイルや PasteDeploy なしでアプリケーションを手動でインスタンス化する
ことも可能です。


.. Now that we know more about how the launcher loads the application, let's look
.. closer at a Pyramid application itself.

ランチャーがどのようにアプリケーションをロードするかについてより詳しいことが
分かったので、次に Pyramid アプリケーション自体により接近して見てみましょう。
