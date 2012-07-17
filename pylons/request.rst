.. Request and Response

リクエストとレスポンス
++++++++++++++++++++++

.. Pylons magic globals

Pylons 特殊グローバル変数
=========================

.. Pylons has several magic globals that contain state data for the current
.. request. Here are the closest Pyramid equivalents:

Pylons は、現在のリクエストに対応する状態データを含むいくつかの特殊な
グローバル変数を持っています。これは、それと最も近い Pyramid の等価物です:


pylons.request

    .. The request URL, query parameters, etc.  In Pyramid it's the ``request``
    .. argument to view functions and ``self.request`` in view methods (if your
    .. class constructor follows the normal pattern). In templates it's
    .. ``request`` or ``req`` (starting in Pyramid 1.3). In pshell or unit tests
    .. where you can't get it any other way, use ``request =
    .. pyramid.threadlocal.get_current_request()``.

    リクエスト URL やクエリパラメーターなど。それは Pyramid ではビュー
    関数に対する ``request`` 引数、およびビューメソッドの中では
    ``self.request`` になります (クラスコンストラクタが通常のパターンに
    従う場合)。テンプレートでは、 ``request`` あるいは ``req`` です
    (Pyramid 1.3 から)。 pshell やユニットテストなどで他の方法では取得
    することができない場合、 ``request =
    pyramid.threadlocal.get_current_request()`` を使用してください。


pylons.response

    .. The HTTP response status and document. Pyramid does not have a global
    .. response object. Instead, your view should create a
    .. ``pyramid.response.Response`` instance and return it. If you're using an
    .. renderer, it will create a response object for you. 

    HTTP レスポンスステータスおよびドキュメント。 Pyramid にはグローバル
    なレスポンスオブジェクトはありません。代わりに、ビューは
    ``pyramid.response.Response`` インスタンスを生成し、それを返す必要が
    あります。レンダラーを使用していれば、自動的にレスポンスオブジェクト
    が生成されます。


    .. For convenience, there's a ``request.response`` object available which you
    .. can set attributes on and return, but it will have effect only if you
    .. return it.  If you're using a renderer, it will honor changes you make to
    .. ``request.response``.

    利便性のために ``request.response`` オブジェクトが用意されています。
    あなたはこのオブジェクトの属性に値を設定して返すことができます。
    ただし、それを返した場合にのみ効果があります。レンダラーを使用して
    いれば、それは ``request.response`` に加えた変更を尊重します。


pylons.session

    .. Session variables. See the Sessions chapter.

    セッション変数。セッションの章を参照してください。


pylons.tmpl_context

    .. A scratch object for request-local data, usually used to pass varables
    .. to the template. In Pyramid, you return a dict of variables and let the
    .. renderer apply them to a template. Or you can render a template yourself in
    .. view code.

    リクエストローカルデータのためのスクラッチオブジェクト。変数を
    テンプレートへ渡すために通常使用されます。 Pyramid では、変数の辞書を
    返してレンダラーがそれをテンプレートに適用するようにします。または
    ビューコードの中で自分でテンプレートをレンダリングすることもできます。


    .. If the view is a method, you can also set instance variables. The view
    .. instance is visible as ``view`` in templates. There are two main use cses
    .. for this. One, to set variables for the site template that would otherwise
    .. have to be in *every* return dict. Two, for variables that are specific to
    .. HTML rendering, when the view is registered with both an HTML renderer and
    .. a non-HTML renderer (e.g., JSON).

    ビューがメソッドである場合、さらに、インスタンス変数をセットするこ
    とができます。ビューインスタンスはテンプレート中で ``view`` として
    見えます。これの主要なユースケースが 2 つあります。 1. サイトテンプレート
    用に変数をセットするため。これ以外の方法では *すべての* 返却される辞書
    の中でセットする必要があります。 2. ビューが HTML レンダラーと非 HTML
    レンダラー (例えば JSON) の両方で登録されている場合に HTML レンダラー
    に特有の変数のため。


    .. Pyramid does have a port of "tmpl_context" at
    .. ``request.tmpl_context``, which is visible in templates as ``c``. However,
    .. it never caught on among Pyramid-Pylons users and is no longer documented.

    Pyramid は "tmpl_context" の移植版である ``request.tmpl_context``
    を持っています。それはテンプレートにおいて ``c`` として見えます。
    しかし、それは Pyramid-Pylons ユーザの間で人気を得ることがなく、
    もはや文書化されません。


pylons.app_globals

    .. Global variables shared across all requests. The nearest equivalent is
    .. ``request.registry.settings``.  This normally contains the application
    .. settings, but you can also store other things in it too.  (The registery is
    .. a singleton used internally by Pyramid.)

    すべてのリクエストを横断して共有されるグローバル変数。最も近い等価物は
    ``request.registry.settings`` です。これは通常アプリケーション設定を
    含んでいますが、他のものを格納することもできます (レジストリは
    Pyramid によって内部で使用されるシングルトンです)。


pylons.cache

    .. A cache object, used to automatically save the results of expensive
    .. calculations for a period of time, across multiple requests. Pyramid has no
    .. built-in equivalent, but you can set up a cache using "pyramid_beaker".
    .. You'll probably want to put the cache in the settings?

    キャッシュオブジェクト。高価な計算の結果を多数のリクエストを横断して
    ある期間の間自動的に保存するために使用されます。 Pyramid は内蔵の
    等価物を持っていませんが、 "pyramid_beaker" を使用してキャッシュを
    セットアップすることができます。恐らくキャッシュを settings に
    入れたいと思うでしょう?


pylons.url

    .. A URL generator. Pyramid's request object has methods that generate URLs.
    .. See also the URL Generator chapter for a convenience object that reduces
    .. boilerplate code.

    URL ジェネレーター。 Pyramid のリクエストオブジェクトは URL を
    生成するためのメソッドを持っています。 boilerplate コードを減らす便利な
    オブジェクトについては、 URL ジェネレーターの章も参照してください。



.. Request and response API

リクエストとレスポンスの API
============================

.. Pylons uses WebOb's request and response objects. Pyramid uses subclasses of
.. these so all the familiar attributes and methods are there: ``params``,
.. ``GET``, ``POST``, ``headers``, ``method``, ``charset``, ``date``, ``environ``,
.. ``body``, and ``body_file``. The most commonly-used attribute is ``params``,
.. which is the query parameters and POST variables.

Pylons は WebOb のリクエストオブジェクトおよびレスポンスオブジェクトを
使用します。 Pyramid は、これらのサブクラスを使用します。したがって、
よく知られている属性およびメソッドはすべてそこにあります: ``params``,
``GET``, ``POST``, ``headers``, ``method``, ``charset``, ``date``,
``environ``, ``body``, and ``body_file`` 。一般に最も使用された属性は
``params`` です。それはクエリパラメーターおよび POST 変数です。


.. Pyramid adds several attributes and methods. ``context``, ``matchdict``,
.. ``matched_route``, ``registry``, ``registry.settings``, ``session``, and
.. ``tmpl_context`` access the request's state data and global application data. 
.. ``route_path``, ``route_url``, ``resource_url``, and ``static_url`` generate
.. URLs.

Pyramid はいくつかの属性および方法を加えます。 ``context``, ``matchdict``,
``matched_route``, ``registry``, ``registry.settings``, ``session``,
``tmpl_context`` はリクエストの状態データおよびグローバルプリケーション
データにアクセスします。 ``route_path``, ``route_url``, ``resource_url``,
``static_url`` は URL を生成します。


.. Rather than repeating the existing documentation for these attributes and
.. methods, we'll just refer you to the original docs:

これらの属性とメソッドに対する既存のドキュメンテーションを繰り返す代わりに、
オリジナルのドキュメントを紹介するに留めましょう:


* `Pyramd Request and Response Objects <http://docs.pylonsproject.org/projects/pyramid/en/latest/narr/webob.html>`_
* `Pyramid Request API <http://docs.pylonsproject.org/projects/pyramid/en/latest/api/request.html#request-module>`_
* `Pyramid Response API <http://docs.pylonsproject.org/projects/pyramid/en/latest/api/response.html>`_
* `WebOb Request API <http://docs.webob.org/en/latest/reference.html#id1>`_
* `WebOb Response API <http://docs.webob.org/en/latest/reference.html#id2>`_


.. Response examples:

レスポンスの例:


::

    response = request.response

    # -OR-
    from pyramid.response import Response
    response = Response

    # In either case.
    response.status = "200 OK"
    response.status_int = 200
    response.content_type = "text/plain"
    response.charset = "utf-8"
    response_headerlist = [
        ("Set-Cookie", "abc=123"), ("X-My-Header", "foo")]
    response_cache_for = 3600    # Seconds
    return response
