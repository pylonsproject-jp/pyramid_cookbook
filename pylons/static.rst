.. Static Files

静的ファイル
++++++++++++

.. In Pylons, the application's "public" directory is configured as a static
.. overlay on "/", so that URL "/images/logo.png" goes to
.. "pylonsapp/public/images/logo.png". This is done using a middleware. Pyramid
.. does not have an exact equivalent but it does have a way to serve static files,
.. and add-on packages provide additional ways.

Pylons では、アプリケーションの "public" ディレクトリは "/" の上の
静的なオーバーレイとして設定されます。その結果、 URL "/images/logo.png"
は "pylonsapp/public/images/logo.png" に行き着きます。これはミドルウェア
を使って行われます。 Pyramid にはこれの厳密な等価物はありませんが、静的
ファイルを返す方法はあります。また、アドオンパッケージが別の方法を提供
しています。


.. Static view

静的ビュー
===========

.. This is Pyramid's default way to serve static files. As you'll remember from
.. the main function in an earlier chapter:

これは Pyramid で静的ファイルを返すデフォルトの方法です。
前の方の章にあった main 関数を思い出してください:


::

    config.add_static_view('static', 'static', cache_max_age=3600)


.. This tells Pyramid to publish the directory "pyramidapp/static" under URL
.. "/static", so that URL "/static/images/logo.png" goes to
.. "pyramidapp/static/images/logo.png". 

これは、ディレクトリ "pyramidapp/static" を URL "/static" の下で公開する
ように Pyramid に伝えます。その結果、 URL "/static/images/logo.png" は
"pyramidapp/static/images/logo.png" に行き着きます。


.. It's implemented using *traversal*, which we haven't talked about much in this
.. Guide. Traversal-based views have a *view name* which serves as a URL prefix or
.. component. The first argument is the view name ("static"), which implies it
.. matches URL "/static". The second argument is the asset spec for the directory
.. (relative to the application's Python package). The keyword arg is an option
.. which sets the HTTP expire headers to 3600 seconds (1 hour) in the future.
.. There are other keyword args for permissions and such.

それは *トラバーサル* を使用して実装されます。トラバーサルに関しては、
このガイドの中ではあまり話していません。トラバーサルベースのビューは
*ビュー名* を持っていて、URL プレフィックスまたはコンポーネントとして
使えます。最初の引数はビュー名 ("static") です。それはこのビューが URL
"/static" とマッチすることを意味しています。第 2 の引数は、 (アプリ
ケーションの Python パッケージからの相対) ディレクトリに対する asset
スペックです。キーワード引数は HTTP expire ヘッダーを 3600 秒 (1時間)
後に設定するオプションです。これ以外にもパーミッションその他のための
キーワード引数があります。


.. Pyramid's static view has the following advantages over Pylons:

Pyramid の静的ビューは、 Pylons に対して次のような利点があります:


.. * It encourages all static files to go under a single URL prefix, so they're
..   not scattered around the URL space.
.. * Methods to generate URLs are provided: ``request.static_url()`` and
..   ``request.static_path()``.
.. * The deployment configuration (INI file) can override the base URL ("/static")
..   to serve files from a separate static media server
..   ("http://static.example.com/").
.. * The deployment configuration can also override items in the static directory,
..   pointing to other subdirectories or files instead. This is called "overriding
..   assets" in the Pyramid manual.

* すべての静的ファイルが単一の URL プレフィックスの下に置かれるように
  強制します。したがって、静的ファイルが URL 空間に散在しません。
* URL を生成するメソッドが提供されます: ``request.static_url()`` と
  ``request.static_path()``
* 別の静的メディアサーバからファイルを返すために、デプロイ設定 (INI ファイル)
  でベース URL ("/static") を上書きすることができます。
* デプロイ設定では、さらに静的ディレクトリの中のアイテムを、他のサブ
  ディレクトリあるいはファイルを代わりに指すことによって上書きすることが
  できます。これは Pyramid マニュアルの中で「asset の上書き」と呼ばれます。


.. It has the following disadvantages compared to Pyramid:

一方 Pyramid と比較して次のような欠点があります:


.. * Static URLs have the prefix "/static". 
.. * It can't serve top-level file URLs such as "/robots.txt" and "/favicon.ico".

* 静的 URLにプレフィックス "/static" が付きます。
* トップレベルのファイル URL を返すことができません。


.. You can serve any URL directory with a static view, so you could have a
.. separate view for each URL directory like this:

静的ビューで任意の URL ディレクトリを返すことが可能です。したがって、以下の
ように各 URL ディレクトリに対する個別のビューを持つことができるでしょう:


::

    config.add_images_view('images', 'static/images')
    config.add_stylesheets_view('stylesheets', 'static/stylesheets')
    config.add_javascript_view('javascript', 'static/javascript')


.. This configures URL "/images" pointing to directory "pyramidapp/static/images",
.. etc. 

これは、例えば URL "/images" をディレクトリ "pyramidapp/static/images" を
指すように設定します。


.. If you're using Pyramid's authorization system, you can also make a separate
.. view for files that require a certain permission:

Pyramid の認証システムを使用していれば、特定のパーミッションを要求する
ファイルに対する別のビューを作ることもできます:


::

    config.add_static_view("private", "private", permission="admin")


.. Generating static URLs

静的 URL の生成
----------------------

.. You can generate a URL to a static file like this:

静的ファイルへの URL をこのように生成することができます:


.. code-block:: mako

   href="${request.static_url('static/images/logo.png')}


.. Top-level file URLs

トップレベルのファイル URL
--------------------------

.. So how do you get around the problem of top-level file URLs? You can register
.. normal views for them, as shown later below. For "/favicon.ico", you can
.. replace it with an HTTP header in your site template:

それで、どのようにしてトップレベルのファイル URL の問題を避ければ良いの
でしょうか?  後述するように、それらに対する通常のビューを登録することが
できます。 "/favicon.ico" については、サイトテンプレート中でそれを HTTP
ヘッダに置き換えることができます:


.. code-block:: html

   <link rel="shortcut icon" href="${request.static_url('pyramidapp:static/favicon.ico')}" />


.. The standard Pyramid scaffolds actually do this. For "/robots.txt", you may
.. decide that this actually belongs to the webserver rather than the application,
.. and so you might have Apache serve it directly like this:

標準の Pyramid scaffold は実際にこれを行います。 "/robots.txt" について
は、実際にはこれがアプリケーションではなくウェブサーバに所属すると決めて、
したがってこのように Apache にそのファイルを直接返させることができます。


.. code-block:: apache

   Alias   /robots.txt   /var/www/static/norobots.txt


.. You can of course have Apache serve your static directory too:

もちろん、 Apache に静的ディレクトリも返させることができます:


.. code-block:: apache

   Alias   /static   /PATH-TO/PyramidApp/pyramidapp/static

   
.. But if you're using mod_proxy you'll have to disable proxying that directory
.. *early* in the virtualhost configuration:

しかし、 mod_proxy を使用していれば、 virtualhost 設定の中の
*前の方* でそのディレクトリのプロキシを無効にしなければならないでしょう:


.. code-block:: apache

   Alias ProxyPass   /static   !


.. If you're using RewriteRule in combination with other path directives like
.. Alias, read the RewriteRule flags documentation (especially "PT" and "F") to
.. ensure the directives cooperate as expected.

Alias のような他のパスディレクティブと RewriteRule を組み合わせて使用
している場合、 RewriteRule フラグドキュメンテーション (特に "PT" および
"F") を読んで、ディレクティブが期待通りに連携するようにしてください。


.. External static media server

外部の静的メディアサーバ
----------------------------

.. To make your configuration flexible for a static media server:

静的メディアサーバに対して設定を柔軟にするために:


.. code-block:: ini

    # In INI file
    static_assets = "static"
    # -OR-
    static_assets = "http://staticserver.com/"


.. Main function::

main 関数::


    config.add_static_view(settings["static_assets"], "zzz:static")


.. Now it will generate "http://mysite.com/static/foo.jpg" or
.. "http://staticserver.com/foo.jpg" depending on the configuration.

すると、それは設定に依存して "http://mysite.com/static/foo.jpg" または
"http://staticserver.com/foo.jpg" を生成するでしょう。


.. Static route

静的 route
============

.. This strategy is available in Akhet. It overlays the static directory on top of
.. "/" like Pylons does, so you don't have to change your URLs or worry about
.. top-level file URLs.

この戦略は Akhet で利用可能です。それは Pylons が行うように "/" の上に
静的なディレクトリを覆います。したがって、 URL を変更したり、トップレベル
のファイル URL について心配したりする必要はありません。


::

    config.include('akhet')
    # Put your regular routes here.
    config.add_static_route('zzz', 'static', cache_max_age=3600)
    # Arg 1 is the Python package containing the static files.
    # Arg 2 is the subdirectory in the package containing the files.


.. This registes a static route matching all URLs, and a view to serve it.
.. Actually, the route will have a predicate that checks whether the file exists,
.. and if it doesn't, the route won't match the URL. Still, it's good practice to
.. register the static route after your other routes. 

これは、すべての URL とマッチする静的 route とその URL を返すビューを
登録します。実際には、 route はファイルが存在するかどうかチェックする
述語を持ち、ファイルが存在しなければ route は URL とマッチしません。
とはいえ、他の route より後に静的 route を登録するのはよいことです。


.. If you have another catchall route before it that might match some static URLs,
.. you'll have to exclude those URLs from the route as in this example:

それより前にいくつかの静的 URL とマッチする別の包括的な route があれば、
この例のように route からそれらの URL を除外しなければならないでしょう。


::

    config.add_route("main", "/{action}",
        path_info=r"/(?!favicon\.ico|robots\.txt|w3c)")
    config.add_static_route('zzz', 'static', cache_max_age=3600)


.. The static route implementation does *not* generate URLs to static files, so
.. you'll have to do that on your own. Pylons never did it very effectively
.. either.

静的 route 実装は、静的ファイルに対する URL を生成 *しません* 。
したがって、それを自分自身でしなければならないでしょう。Pylons も
あまり効果的にそれをしませんでした。


.. Other ways to serve top-level file URLs

トップレベルのファイル URL を返す別の方法
=========================================

.. If you're using the static view and still need to serve top-level file URLs,
.. there are several ways to do it.

静的ビューを使用していて、あくまでトップレベルのファイル URL を返す必要
があれば、それをするための方法はいくつかあります。


.. A manual file view

手作業によるファイルビュー
--------------------------

.. This is documented in the Pyramid manual in the Static Assets chapter.

これは Pyramid マニュアルの静的 asset の章でドキュメント化されています。


::

    # Main function.
    config.add_route("favicon", "/favicon.ico")

    # Views module.
    import os
    from pyramid.response import FileResponse

    @view_config(route_name="favicon")
    def favicon_view(request):
        here = os.path.dirname(__file__)
        icon = os.path.join(here, "static", "favicon.ico")
        return FileResponse(icon, request=request


.. Or if you're really curious how to configure the view for traversal without a
.. route:

または、 route のないトラバースに対してビューを設定する方法がとても気に
なる場合:


::

    @view_config(name="favicon.ico")


pyramid_assetviews
------------------

.. "pyramid_assetviews" is a third-party package for top-level file URLs.

"pyramid_assetviews" はトップレベルのファイル URL のためのサードパーティ
のパッケージです。


::

    # In main function.
    config.include("pyramid_assetviews")
    config.add_asset_views("static", "robots.txt")  # Defines /robots.txt .

    # Or to register multiple files at once.
    filenames = ["robots.txt", "humans.txt", "favicon.ico"]
    config.add_asset_views("static", filenames=filenames, http_cache=3600)


.. Of course, if you have the files in the static directory they'll still be
.. visible as "/static/robots.txt" as well as "/robots.txt". If that bothers you,
.. make another directory outside the static directory for them.

もちろん、ファイルが static ディレクトリの中にあれば、それらは
"/robots.txt" だけでなく "/static/robots.txt" としても見えるでしょう。
これが問題となる場合、 static ディレクトリの外にこれらのファイルのための
別のディレクトリを作成してください。

