.. Views

ビュー
++++++

.. The biggest difference between Pyramid and Pylons is how views are structured,
.. and how they invoke templates and access state variables. This is a large topic
.. because it touches on templates, renderers, request variables, URL generators,
.. and more, and several of these topics have many facets. So we'll just start
.. somewhere and keep going, and let it organize itself however it falls.

Pyramid と Pylons の間の最も大きな違いは、ビューがどのように構造化されるか、
そしてビューがどのようにテンプレートを起動して状態変数にアクセスするかです。
これは、テンプレート、レンダラー、リクエスト変数、 URL ジェネレータなどに
関係するため大きなトピックです。また、これらのトピックのうちのいくつかには
多くの側面があります。したがって、とにかく始めてそのまま進み、最終的に
それを体系化することにします。


.. First let's review Pylons' view handling.  In Pylons, a view is called an
.. "action", and is a method in a controller class. Pylons has specific rules
.. about the controller's module name, class name, and base class. When Pylons
.. matches a URL to a route, it uses the routes 'controller' and 'action'
.. variables to look up the controller and action. It instantiates the controller
.. and calls the action. The action may take arguments with the same name as
.. routing variables in the route; Pylons will pass in the current values from the
.. route. The action normally returns a string, usually by calling
.. ``render(template_name)`` to render a template. Alternatively, it can return a
.. WebOb ``Response``. The request's state data is handled by magic global
.. variables which contain the values for the current request. (This includes
.. equest parameters, response attributes, template variables, session variables,
.. URL generator, cache object, and an "application globals" object.)

最初に Pylons のビューの取り扱いを確認しましょう。 Pylons では、ビュー
は「アクション」と呼ばれ、コントローラクラスのメソッドです。 Pylons は
コントローラのモジュール名、クラス名およびベースクラスに関する特有の
規則を持っています。 Pylons がルートに URL をマッチさせる場合、それは
コントローラとアクションを調べるために routes の 'controller' と 'action'
変数を使用します。それはコントローラをインスタンス化し、アクションを
呼び出します。アクションは route の中のルーティング変数と同じ名前の
引数を受け取ることができます; Pylons は route から現在の値を渡します。
アクションは通常文字列を返します。これは通常はテンプレートをレンダリング
するために ``render(template_name)`` を呼び出すことによって行われます。
あるいは、アクションは WebOb ``Response`` を返すこともできます。
リクエストの state データは、現在のリクエストに対する値を含んでいる
特殊なグローバル変数によって扱われます (これはリクエストパラメータ、
レスポンス属性、テンプレート変数、セッション変数、 URL ジェネレータ、
キャッシュオブジェクト、「アプリケーショングローバル」オブジェクトが
含まれます)。


.. View functions and view methods

ビュー関数とビューメソッド
===============================

.. A Pyramid *view callable* can be a function or a method, and it can be in any
.. location. The most basic form is a function that takes a request and returns a
.. response:

Pyramid の *ビュー callable* は、関数またはメソッドです。また、それは
任意の場所に置くことができます。最も基本的な形式は、 request を受け取って
response を返す関数です:


::

    from pyramid.response import Response

    def my_view(request):
        return Response("Hello, world!")


.. A view method may be in any class. A class containing view methods is
.. conventionally called a "view class" or a "handler". If a view is a method, the
.. request is passed to the class constructor, and the method is called without
.. arguments.

ビューメソッドは任意のクラスの中に置くことができます。ビューメソッドを
含むクラスは、慣例的に「ビュークラス」あるいは「ハンドラ」と呼ばれます。
ビューがメソッドの場合、 request はクラスコンストラクタに渡され、
メソッドは引数なしで呼ばれます。


::

    class MyHandler(object):
        def __init__(self, request):
            self.request = request

        def my_view(self):
            return Response("Hello, classy world!")

.. The Pyramid structure has three major benefits.

Pyramid 構造には 3 つの大きな利点があります。


.. * Most importantly, it's easier to test. A unit test can call a view with a
..   fake request, and get back the dict that would have been passed
..   to the template. It can inspect the data variables directly rather than
..   parsing them out of the HTML.

* 最も重要なこととして、それはテストするのがより簡単です。ユニットテストは
  ダミーのリクエストでビューを呼び、テンプレートに渡されるはずの
  辞書を受け取ることができます。それによって、データ変数を HTML からパース
  することなく直接調査することができます。


.. * It's simpler and more modular. No magic globals.

* それは単純で、よりモジュール性が高いです。魔法のグローバル変数はありません。


.. * You have the freedom to organize views however you like.

* ビューを好きなように構造化する自由があります。


.. Typical view usage

典型的なビューの使用方法
========================

.. Merely defining a view is not enough to make Pyramid use it. You have to
.. *register* the view, either by calling ``config.add_view()`` or using the
.. ``@view_config`` decorator.

単にビューを定義するだけでは、それを Pyramid に使用させるのに不十分です。
``config.add_view()`` を呼ぶか ``@view_config`` デコレータを使用して、
ビューを登録しなければなりません。


.. The most common way to use views is with the ``@view_config`` decorator. This
.. both marks the callable as a view and allows you to specify a template. It's
.. also common to define a base class for common code shared by view classes. The
.. following is borrowed from the Akhet demo.

ビューを使用する最も一般的な方法は ``@view_config`` デコレータを使う
ことです。これは、 callable をビューとしてマークして、テンプレートを
指定できるようにします。さらに、ビュークラスによって共有される共通
コードのためのベースクラスを定義することも一般的です。以下は Akhet
デモから借用したコードです。


::

    from pyramid.view import view_config

    class Handler(object):
        def __init__(self, request):
            self.request = request

    class Main(Handler):

        @view_config(route_name="home", renderer="index.mako")
        def index(self):
            return {"project": "Akhet Demo"}


.. The application's main function has a ``config.scan()`` line, which imports all
.. application modules looking for ``@view_config`` decorators. For each one it calls
.. ``config.add_view(view)`` with the same keyword arguments. The scanner also
.. recognizes a few other decorators which we'll see later. If you know that all
.. your views are in a certain module or subpackage, you can scan only that one:
.. ``config.scan(".views")``.

アプリケーションの main 関数には ``config.scan()`` 行があり、
``@view_config`` デコレータを探してすべてのアプリケーションモジュールを
インポートします。 ``@view_config`` デコレータそれぞれに対して、それと同じ
キーワード引数を伴って ``config.add_view(view)`` が呼ばれます。スキャナは
さらに後で見る他のいくつかのデコレータも認識します。すべてのビューが特定の
モジュールまたはサブパッケージに存在することを知っていれば、それだけを
スキャンすることができます: ``config.scan(".views")`` 。


.. The example's ``@view_config`` decorator has two arguments, 'route_name' and
.. 'renderer'. The 'route_name' argument is *required* when using URL dispatch, to tell
.. Pyramid which route should invoke this view. The "renderer" argument names a
.. template to invoke. In this case, the view's return value is a dict of data
.. variables for the template. (This takes the place of Pylons' 'c' variable, and
.. mimics TurboGears' usage pattern.) The renderer takes care of creating a
.. Response object for you.

例における ``@view_config`` デコレータは、 2 つの引数 'route_name' と
'renderer' を持っています。 URL ディスパッチを使用する場合、どの route
がこのビューを起動すべきかを Pyramid に伝えるために 'route_name' 引数は
*必須* です。 "renderer" 引数は、起動すべきテンプレートを指定します。
この場合、ビューの戻り値はテンプレート用データ変数の辞書です (これは
Pylons の 'c' 変数に代わるもので、 TurboGears の使用パターンを模倣します)。
レンダラーは、レスポンスオブジェクトを生成する面倒を見ます。

        
.. View configuration arguments

ビュー設定引数
============================

.. The following arguments can be passed to ``@view_config`` or
.. ``config.add_view``.  If you have certain argument values that are the same for
.. all of the views in a class, you can use ``@view_defaults`` on the *class* to
.. specify them in one place.

以下の引数を ``@view_config`` または ``config.add_view`` に渡すことが
できます。クラスに含まれるすべてのビューでいくつかの引数の値が同じなら、
それらを 1 箇所で指定するために *クラス* に対して ``@view_defaults``
を使用することができます。


.. This list includes only arguments commonly used in Pylons-like applications.
.. The full list is in `View Configuration`_ in the Pyramid manual. The arguments
.. have the same predicate/non-predicate distinction as ``add_route`` arguments.
.. It's possible to register multiple views for a route, each with different
.. predicate arguments, to invoke a different view in different circumstances.

このリストは Pylons 風のアプリケーションで一般的に使用される引数だけを
含んでいます。完全なリストは Pyramid マニュアルの `View Configuration`_
にあります。引数には ``add_route`` 引数と同じく述語/非述語の区別があります。
異なる状況で異なるビューを起動するために、 1 つのルートに対して複数のビュー
を各々異なる述語引数と共に登録することができます。


.. Some of the arguments are common to ``add_route`` and ``add_view``. In the
.. route's case it determines whether the route will match a URL. In the view's
.. case it determines whether the view will match the route.

引数のうちいくつかは ``add_route`` と ``add_view`` で共通です。
route のケースでは、 route が URL とマッチするかどうかを判断します。
ビューのケースでは、ビューがルートとマッチするかどうかを判断します。


route_name

    .. .[predicate] The route to attach this view to. Required when using URL
    .. dispatch.

    [述語引数] このビューを取り付けるルート。 URL ディスパッチを使用する
    場合は必須です。


renderer

    .. .[non-predicate] The name of a renderer or template. Discussed in Renderers
    .. below.

    [非述語引数] レンダラーまたはテンプレートの名前。下記のレンダラーの
    項目で議論されます。


permission

    .. .[non-predicate] A string naming a permission that the current user must
    .. have in order to invoke the view. 

    [非述語引数] 現在のユーザーがビューを起動するために持っていなければ
    ならないパーミッションを指定する文字列。


http_cache

    .. .[non-predicate] Affects the 'Expires' and 'Cache-Control' HTTP headers in
    .. the response.  This tells the browser whether to cache the response and for
    .. how long.  The value may be an integer specifying the number of seconds to
    .. cache, a ``datetime.timedelta`` instance, or zero to prevent caching. This
    .. is equivalent to calling ``request.response.cache_expires(value)`` within
    .. the view code.

    [非述語引数] レスポンスの 'Expires' および 'Cache-Control' HTTP ヘッダ
    に影響します。これは、レスポンスをキャッシュすべきかどうか、そしてそれは
    どれくらいの期間かをブラウザに伝えます。値は、キャッシュする秒数を
    指定する整数、 ``datetime.timedelta`` インスタンス、あるいはキャッシュ
    することを防ぐ 0 のいずれかです。これはビューコード内で
    ``request.response.cache_expires(value)`` を呼ぶことと等価です。


context

    .. .[predicate] This view will be chosen only if the *context* is an instance
    .. of this class or implements this interface. This is used with traversal,
    .. authorization, and exception views.

    [述語引数] *context* がこのクラスのインスタンスか、このインタフェース
    を実装する場合のみ、このビューが選ばれます。これはトラバーサル、認可
    および例外ビューと共に使用されます。


request_method

    .. .[predicate] One of the strings "GET", "POST", "PUT", "DELETE', "HEAD".
    .. The request method must equal this in order for the view to be chosen.
    .. REST applications often register multiple views for the same route, each
    .. with a different request method.

    [述語引数] 文字列 "GET", "POST", "PUT", "DELETE', "HEAD" のうちの 1 つ。
    ビューが選ばれるためには、リクエストメソッドはこれと等しくなければ
    なりません。 REST アプリケーションは、しばしば同じルートに対して
    異なるリクエストメソッドと共に複数のビューを登録します。


request_param

    .. .[predicate] This can be a string such as "foo", indicating that the request
    .. must have a query parameter or POST variable named "foo" in order for
    .. this view to be chosen. Alternatively, if the string contains "=" such as
    .. "foo=1", the request must both have this parameter *and* its value must be
    .. as specified, or this view won't be chosen.

    [述語引数] これは "foo" のような文字列にすることが可能で、その場合
    ビューが選ばれるためにリクエストが "foo" という名前のクエリパラメータ
    または POST 変数を持っていなければならないことを示しています。あるいは
    文字列が "foo=1" のように "=" を含んでいる場合、リクエストはこの
    パラメータを含んでいなければならず、 *かつ* その値は指定された値と
    等しくなければなりません。そうでなければ、このビューは選ばれません。


match_param

    .. .[predicate] Like request_param but refers to a routing variable in the
    .. matchdict. In addition to the "foo" and "foo=1" syntax, you can also pass a
    .. dict of key/value pairs: all these routing variables must be present and have
    .. the specified values.

    [述語引数] request_param と似ていますが、 matchdict の中のルーティング
    変数を参照します。 "foo" と "foo=1" 構文に加えて、キー/値ペアの辞書
    を渡すことができます: これらのルーティング変数はすべて存在しなければ
    ならず、指定された値を持っていなければなりません。


xhr, accept, header, path_info

    .. .[predicate] These work like the corresponding arguments to
    .. ``config.add_route``.

    [述語引数] これらは ``config.add_route`` の対応する引数と同じように
    機能します。


custom_predicates

    .. .[predicate] The value is a list of functions. Each function should take a
    .. ``context`` and ``request`` argument, and return true or false whether the
    .. arguments are acceptable to the view. The view will be chosen only if all
    .. functions return true. Note that the function arguments are different than
    .. the corresponding option to ``config.add_route``.

    [述語引数] 値は関数のリストです。それぞれの関数は ``context`` と
    ``request`` の引数を受け取り、ビューが引数を受理可能かどうかによって
    true または false を返します。すべての関数が true を返す場合のみビュー
    が選ばれます。関数引数が ``config.add_route`` の対応するオプションとは
    異なることに注意してください。


.. One view option you will *not* use with URL dispatch is the "name" argument.
.. This is used only in traversal.

URL ディスパッチと共に使用 *しない* ビューオプションの 1 つは "name"
引数です。これはトラバーサルの中でのみ使用されます。


.. Renderers

レンダラー
==========

.. A *renderer* is a post-processor for a view. It converts the view's return
.. value into a Response. This allows the view to avoid repetitive boilerplate
.. code. Pyramid ships with the following renderers: Mako, Chameleon, String,
.. JSON, and JSONP. The Mako and Chameleon renderers takes a dict, invoke the
.. specified template on it, and return a Response. The String renderer converts
.. any type to a string. The JSON and JSONP renderers convert any type to JSON or
.. JSONP. (They use Python's ``json`` serializer, which accepts a limited variety
.. of types.)

*レンダラー* はビューのためのポストプロセッサです。それはビューの戻り値
をレスポンスに変換します。これにより、ビューの中で決まりきった boilerplate
コードを避けることができます。 Pyramid は次に挙げるレンダラーを搭載して
います: Mako, Chameleon, 文字列, JSON および JSONP 。 Mako と Chameleon
レンダラーは、辞書を受け取り、指定されたテンプレートを起動し、レスポンスを
返します。文字列レンダラーは任意の型を文字列に変換します。 JSON と JSONP
レンダラーは、任意の型を JSON または JSONP に変換します (Python の ``json``
シリアライザを使用します。それは、限定された種類の型を受け付けます)。


.. The non-template renderers have a constant name: ``renderer="string"``,
.. ``renderer="json"``, ``renderer="jsonp"``. The template renderers are invoked
.. by a template's filename extension, so ``renderer="mytemplate.mako"`` and
.. ``renderer="mytemplate.mak"`` go to Mako. Note that you'll need to specify a
.. Mako search path in the INI file or main function:

非テンプレートレンダラーは固定の名前を持っています: ``renderer="string"``,
``renderer="json"``, ``renderer="jsonp"`` 。
テンプレートレンダラーはテンプレートのファイル名拡張子によって起動されます。
そのため ``renderer="mytemplate.mako"`` や ``renderer="mytemplate.mak"``
によって Mako が起動されます。 INI ファイルか main 関数で Mako 検索パスを
指定する必要があることに注意してください:


.. code-block:: ini

   [app:main]
   mako.directories = my_app_package:templates


.. Supposedly you can pass an asset spec rather than a relative path for the Mako
.. renderer, and thus avoid defining a Mako search path, but I couldn't get it to
.. work. Chameleon templates end in .pt and must be specified as an asset spec.

Mako レンダラーに相対パスではなく asset スペックを渡すことができるはずで、
そうすれば Mako 検索パスの定義を避けることができますが、私はそれを動作
させることができませんでした (訳注: Pyramid 1.3.2 には Mako ルックアップに
関して不具合がある https://github.com/Pylons/pyramid/issues/606 )。
Chameleon テンプレートは .pt で終わり、必ず asset スペックとして指定されます。


.. You can register third-party renderers for other template engines, and you can
.. also re-register a renderer under a different filename extension.  The Akhet
.. demo has an example of making pyramid send templates ending in .html through Mako.

他のテンプレートエンジン用にサードパーティのレンダラーを登録することが
できます。また、異なるファイル名拡張子に対してレンダラーを再登録する
こともできます。 Akhet デモには .html で終わるテンプレートを Mako を渡す
ように Pyramid を設定する例があります。


.. You can also invoke a renderer inside view code.

また、ビューコードの内部でレンダラーを起動することができます。


::

    from pyramid.renderers import render, render_to_response

    variables = {"dear": "Mr A", "sincerely": "Miss Z", 
        "date": datetime.date.today()}

    # Render a template to a string.
    letter = render("form_letter.mako", variables, request=self.request)

    # Render a template to a Response object.
    return render_to_response("mytemplate.mako", variables,
        request=self.request)


.. Debugging views

ビューのデバッグ
================

.. If you're having trouble with a route or view not being chosen when you think
.. it should be, try setting "pyramid.debug_notfound" and/or
.. "pyramid.debug_routematch" to true in *development.ini*. It will log its
.. reasoning to the console.

選択されるはずだと思うルートまたはビューが選択されないという問題を
抱えている場合は、 *development.ini* の中で "pyramid.debug_notfound" と
"pyramid.debug_routematch" のどちらかまたは一方を true に設定してみて
ください。それによって推論結果がコンソールに記録されるようになります。


.. Multiple views using the same callable

同じ callable を使用した複数のビュー
======================================

.. You can stack multiple ``@view_config`` onto the same view method or
.. function, in cases where the templates differ but the view logic is the 
.. same. 

テンプレートが異なってもビューロジックが同じという場合には、同じビュー
メソッドや関数上に複数の ``@view_config`` を積み重ねることができます。


::

        @view_config(route_name="help", renderer="help.mak")
        @view_config(route_name="faq", renderer="faq.mak")
        @view_config(route_name="privacy", renderer="privacy_policy.mak")
        def template(request):
            return {}

        @view_config(route_name="info", renderer="info.mak")
        @view-config(route_name="info_json", renderer="json")
        def info(request):
            return {}


.. include::  ../links.rst

.. _View Configuration:  http://docs.pylonsproject.org/projects/pyramid/en/latest/narr/viewconfig.html#view-configuration-parameters
