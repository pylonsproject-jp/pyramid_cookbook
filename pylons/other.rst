.. Other Pyramid Features

他の Pyramid 機能
++++++++++++++++++++++

.. Shell

シェル
======

.. Pyramid has a command to preload your application into an interactive Python
.. prompt. This can be useful for debugging or experimentation. The command is
.. "pshell", akin to "paster shell" in Pylons.

Pyramid は、アプリケーションを対話型の Python プロンプトの中に preload
するコマンドを持っています。これはデバッグまたは実験の役に立ちます。
そのコマンドは "pshell" で、 Pylons における "paster shell" の親戚です。


.. code-block:: sh

   $ pshell development.ini
   Python 2.6.5 (r265:79063, Apr 29 2010, 00:31:32)
   [GCC 4.4.3] on linux2
   Type "help" for more information.

   Environment:
     app          The WSGI application.
     registry     Active Pyramid registry.
     request      Active request object.
     root         Root of the default resource tree.
     root_factory Default root factory used to create `root`.

   >>>


.. It doesn't initialize quite as many globals as Pylons, but ``app`` and
.. ``request`` will be the most useful.

それは Pylons ほど多くのグローバル変数を初期化しませんが、 ``app`` と
``request`` は最も有用なものになるでしょう。


.. Other commands

他のコマンド
==============

.. Other commands available:

利用可能な他のコマンド:


.. * proutes: list the application's routes. (Akin to Pylons "paster routes".)
.. * pviews: list the application's views.
.. * ptweens: list the application's tweens.
.. * prequest: load the application, process a specified URL, and print the
..   response body on standard output.

* proutes: アプリケーションの route をリストする。
  (Pylons の "paster routes" の親戚。)
* pviews: アプリケーションのビューをリストする。
* ptweens: アプリケーションの tween をリストする。
* prequest: アプリケーションをロードして、指定された URL を処理して、
  レスポンスボディを標準出力に表示する。


.. Forms

フォーム
========

.. Pyramid does not include a form library. Pylons includes WebHelpers for form
.. generation and FormEncode for validation and error messages. These work under
.. Pyramid too. However, there's no built-in equivalent to Pylons' ``@validate``
.. decorator. Instead we recommend the "pyramid_simpleform_" package, which
.. replaces @validate with a more flexible structure. 

Pyramid はフォームライブラリを含んでいません。 Pylons は、フォーム生成の
WebHelpers と、バリデーションとエラーメッセージ用の FormEncode を
含んでいます。これらは Pyramid の下でも動きます。しかし、 Pylons の
``@validate`` デコレータに相当する内蔵の等価物はありません。代わりに、
私たちは "pyramid_simpleform_" パッケージを推奨します。それは @validate
をよりフレキシブルな構造に置き換えます。


.. There are several other form libraries people use with Pyramid. These are
.. discussed in the regular Forms_ section in the Pyramid Cookbook.

Pyramid と共に使われる他のフォームライブラリがいくつかあります。
それらは Pyramid Cookbook 中の正規の Forms_ セクションで議論されます。


WebHelpers
==========

.. WebHelpers is a third-party package containing HTML tag builders, text
.. functions, number formatting and statistical functions, and other generic
.. functions useful in templates and views. It's a Pylons dependency but is
.. optional in Pyramid.

WebHelpers は、 HTML タグビルダー、テキスト処理機能、数値フォーマットと
統計機能、およびテンプレートとビューに役立つその他の汎用的な機能を含む
サードパーティパッケージです。それは Pylons の依存パッケージですが、
Pyramid においてはオプションです。


.. The ``webhelpers.pylonslib`` subpackage does not work with Pyramid because it
.. depends on Pylons' special globals. ``webhelpers.mimehelper`` and
.. ``webhelpers.paginate`` have Pylons-specific features that are disabled under
.. other frameworks.  WebHelpers has not been tested on Python 3.

``webhelpers.pylonslib`` サブパッケージは、 Pylons の特殊グローバル変数
に依存するので Pyramid では動作しません。 ``webhelpers.mimehelper`` と
``webhelpers.paginate`` には ``Pylons`` に特有の機能があり、これは
他のフレームワークの下では無効になります。 WebHelpers は Python 3 上で
テストされていません。


.. The next version of WebHelpers may be released as a different distribution 
.. (WebHelpers2) with a subset of the current helpers ported to Python 3. It will
.. probably spin off Paginate and the Feed Generator to separate distribitions.

WebHelpers の次のバージョンでは、現在の helpers の部分集合が Python 3
に移植されて、異なるディストリビューション (WebHelpers2) としてリリース
されるでしょう。 Paginate と Feed Generator は、おそらく別のディストリ
ビューションとして独立します。


.. Events

イベント
========

.. The events framework provides hooks where you can insert your own code into the
.. request-processing sequence, similar to how Apache modules work. It standarizes
.. some customizations that were provided ad-hoc in Pylons or not at all. To use
.. it, write a callback function for one of the event types in ``pyramid.events``:
.. ``ApplicationCreated``, ``ContextFound``, ``NewResponse``, ``BeforeRender``.
.. The callback takes an event argument which is specific to the event type.
.. You can register the event with ``@asubscriber`` or
.. ``config.add_subscriber()``.  The Akhet demo has examples.

イベントフレームワークは、リクエスト処理シーケンスに独自のコードを挿入
することができるフックを提供します (それは Apache モジュールの動作に
似ています)。イベントは、Pylons において ad-hoc に提供されていた、
もしくは全く提供されていなかったいくつかのカスタマイズを標準化します。
イベントを使用するためには、 ``pyramid.events`` の中のイベントタイプ
(``ApplicationCreated``, ``ContextFound``, ``NewResponse``,
``BeforeRender``) のうちの 1 つに対してコールバック関数を書いてください。
コールバックは、イベントタイプに特有の event 引数を受け取ります。
``@subscriber`` または ``config.add_subscriber()`` でイベントを登録
することができます。 Akhet デモに例があります。


.. For more details see:

より詳細については、以下を見てください:


* `Using Events * <http://docs.pylonsproject.org/projects/pyramid/en/latest/narr/events.html>`_
* `Using The Before Render Event <http://docs.pylonsproject.org/projects/pyramid/en/latest/narr/hooks.html#using-the-before-render-event>`_
* `pyramid.event API <http://docs.pylonsproject.org/projects/pyramid/en/latest/api/events.html>`_

.. URL generation

URL 生成
==============

.. Pyramid does not come with a URL generator equivalent to "pylons.url".
.. Individual methods are available on the Request object to generate specific
.. kinds of URLs. Of these, route_url covers the normal case of generating a route
.. by name:

Pyramid には "pylons.url" と等価な URL ジェネレータは付属していません。
特定の種類の URL を生成するために、 Request オブジェクトの個別のメソッド
が利用可能です。これらのうち、 route_url は route を名前によって生成する
通常のケースをカバーします:


::

    request.route_url("route_name", variable1="value1")
    request.route_path("route_name", variable1="value1")
    request.route_url("search", _query={"q": "search term"}, _anchor="results")


.. As with all the \*_url vs \*_path methods, ``route_url`` generates an absolute
.. URL, while ``route_path`` generates a "slash" URL (without the scheme or host).
.. The ``_query`` argument is a dict of query parameters (or a sequence of
.. key-value pairs). The ``_anchor`` argument makes a URL with a "#results"
.. fragment. Other special keyword arguments are ``_scheme``, ``_host``,
.. ``_port``, and ``_app_url``.

すべての \*_url と \*_path メソッドの違いと同じく、 ``route_url`` は
絶対 URL を生成する一方で ``route_path`` は「スラッシュ」 URL (スキームや
ホストが付かない) を生成します。 ``_query`` 引数は、クエリパラメータ
(あるいは key-value ペアのシーケンス) の辞書です。 ``_anchor`` 引数は
"#results" 断片と共に URL を生成します。他の特別なキーワード引数は、
``_scheme``, ``_host``, ``_port`` および ``_app_url`` です。


.. The advantage of using these methods rather than hardcoding the URL, is that it
.. automatically addds the application prefix (which may be something more than
.. "/" if the application is mounted on a sub-URL).

URL をハードコーディングするのではなくこれらのメソッドを使用する利点は、
自動的にアプリケーションプレフィックスが追加されることです
(アプリケーションがサブ URL にマウントされている場合、それは "/" 以外に
なるかもしれません)。


.. You can also pass additional positional arguments, and they will be appended
.. to the URL as components. This is not very useful with URL dispatch, it's more
.. of a traversal thing.

さらに、追加の位置引数を渡すこともでき、それらはコンポーネントとして
URL に追加されます。これは URL ディスパッチではあまり有用ではありません。
それはよりトラバーサル的なものです。


.. If the route is defined with a *pregenerator*, it will be called with the
.. positional and keyword arguments, and can modify them before the URL is
.. generated.

route が *pregenerator* と共に定義されれば、 URL が生成される前にそれが
位置およびキーワード引数と共に呼び出され、引数を修正することができます。


.. Akhet has a URLGenerator class, which you can use as shown in the Akhet demo to
.. make a ``url`` variable for your templates, using an event subscriber. Then you
.. can do things like this:

Akhet は URLGenerator クラスを持っています。 Akhet デモの中で示される
ように、イベントサブスクライバを用いてそれを使用してテンプレートに
``url`` 変数を作ることができます。そうすると、このようなことが可能に
なります:


::

    url.route("route_name")          # Generate URL by route name.
    url("route_name")                # The same.
    url.app                          # The application's top-level URL.
    url.current()                    # The current request URL. (Used to
                                     # link to the same URL with different
                                     # match variables or query params.)


.. You can also customize it to do things like this:

さらに、それをカスタマイズしてこんなこともできます:


::

    url.static("images/logo.png")    
    url.image("logo.png")            # Serve an image from the images dir.
    url.deform("...")                # Static file in the Deform package.


.. If "url" is too long for you, you can even name it "u"!

"url" が長すぎると思えば、それを "u" と命名することさえできます!


.. Utility scripts

ユーティリティスクリプト
========================

.. Pyramid has a documented way to write utility scripts for maintenance and the
.. like. See `Writing a Script`_.

Pyramid には、メンテナンスその他のためにユーティリティスクリプトを書く
ためのドキュメント化された方法があります。 `Writing a Script`_ を参照し
てください。


.. Testing

テスト
=======

.. Pyramid makes it easier to write unit tests for your views. 

Pyramid は、ビューのためのユニットテストを書くことがより簡単です。


.. (XXX Need a comparison example.)

(XXX 比較の例が必要。)


.. Internationalization

国際化
====================

.. Pyramid has support for internationalization. At this time it's documented
.. mainly for Chameleon templates, not Mako.

Pyramid には国際化サポートがあります。現時点では、それは主として Mako で
はなく Chameleon テンプレートに対してドキュメント化されています。


.. Higher-level frameworks

高レベルフレームワーク
=======================

.. Pyramid provides a flexible foundation to build higher-level frameworks on.
.. Several have already been written.  There are also application scaffolds and
.. tarballs.

Pyramid は、その上により高レベルのフレームワークを構築するための柔軟な
基礎を提供します。いくつかのものは既に書かれています。さらにアプリケーション
scaffold と tar ボールがあります。


.. * Kotti_ is a content management system that both works out of the box and can
..   be extended. 
.. * Ptah_ is a framework that aims to have as many features as Django. (But no
..   ponies, and no cowbells.) It has a minimal CMS component.
.. * Khufu_ is a suite of scaffolds and utilities for Pyramid.
.. * The Akhet_ demo we have mentioned before. It's a working application in a
..   tarball that you can copy code from.

* Kotti_ は、そのまま使うことも拡張することもできるコンテンツ管理システムです。
* Ptah_ は Django と同数の機能を持つことを目標とするフレームワークです
  (しかし ponies も cowbell もありません)。 それには最小の CMS コンポーネント
  があります。
* Khufu_ は Pyramid 用の scaffold およびユーティリティ一式です。
* これまでに言及した Akhet_ デモ。それは、あなたがコードをコピーすることが
  できる tar ボールに入った動くアプリケーションです。


.. At the opposite extreme, you can make a tiny Pyramid application in 14 lines of
.. Python without a scaffold.  The Pyramid manual has an example: `Hello World`_.
.. This is not possible with Pylons -- at least, not without distorting it
.. severely.

逆の極限では、 scaffold なしで Python 14 行で小さな Pyramid アプリケーション
を作ることができます。 Pyramid マニュアルに例があります: `Hello World`_ 。
これは Pylons では不可能です -- 少なくとも、それを激しく歪めない限りは。


.. include::  ../links.rst

.. _Forms: ../forms/index.html
.. _Writing a Script: http://docs.pylonsproject.org/projects/pyramid/en/latest/narr/commandline.html?highlight=pshell#writing-a-script
.. _Hello World: http://docs.pylonsproject.org/projects/pyramid/en/latest/narr/firstapp.html#hello-world
