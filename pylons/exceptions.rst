.. Exceptions, HTTP Errors, and Redirects

例外、 HTTP エラー、リダイレクト
++++++++++++++++++++++++++++++++++++++


.. Issuing redirects and HTTP errors

リダイレクトの実行と HTTP エラー
=================================

.. Here's how to send redirects and HTTP errors in Pyramid compared to Pylons:

これは、 Pylons と比較して Pyramid でどうやってリダイレクトと HTTP エラー
を送るかを示したものです:


::

    # Pylons -- in controller action
    from pylons.controllers.util import abort, redirect
    abort(404)   # Not Found
    abort(403)   # Forbidden
    abort(400)   # Bad request; e.g., invalid query parameter
    abort(500)   # Internal server error
    redirect(url("section1"))   # Redirect (default 302 Found)

    # Pyramid -- in view code
    import pyramid.httpexceptions as exc
    raise exc.exception_response(400)   # Not Found
    raise exc.HTTPNotFound()            # Same thing
    return exc.HTTPNotFound()           # Same thing
    raise exc.HTTPForbidden()
    raise exc.HTTPBadRequest()
    raise exc.HTTPInernalServerError()
    raise exc.HTTPFound(request.route_url("section1"))   # Redirect


.. The ``pyramid.httpexceptions`` module has classes for all official HTTP
.. statuses. These classes inherit from both ``Response`` and ``Exception``, so
.. you can either return them or raise them.  Raising HTTP exceptions can make
.. your code structurally more readable. It's particularly useful in
.. subroutines where it can cut through several calling stack frames that would
.. otherwise each need an ``if`` to pass the error condition through.

``pyramid.httpexceptions`` モジュールにはすべての公式 HTTP ステータスに
対応したクラスがあります。これらのクラスは ``Response`` と ``Exception``
の両方から継承します。したがって、戻り値として返すことも、例外として
送出することもできます。 HTTP 例外を投げることでコードは構造的により
読みやすくなるかもしれません。それはいくつかの呼び出しスタックフレーム
を通過することがあるサブルーチンで特に役立ちます。そうでなければエラー
状態を受け渡すために各々の呼び出し毎に ``if`` を必要とするでしょう。


.. Exception rules:

例外ルール:


.. 1. Pyramid internally raises ``HTTNotFound`` if no route matches the request,
..    or if no view matches the route and request. It raises ``HTTPForbidden`` if the
..    request is denied based on the current authorization policy.

1. route がリクエストにマッチしない場合、あるいは route とリクエストに
   マッチするビューがない場合、 Pyramid は内部で ``HTTNotFound`` を上げます。
   現在の認可ポリシーに基づいてリクエストが拒否される場合、 Pyramid は
   ``HTTPForbidden`` を上げます。


.. 2. If an uncaught exception occurs during request processing, Pyramid will catch it 
..    and look for an "exception view" that matches it.  An exception view is one
..    whose *context* argument is the exception's class, an ancestor of it, or an
..    interface it implements.  All other view predicates must also match;
..    e.g., if a 'route_name' argument is specified it must match the actual route
..    name. (Thus an exception view is typically registered *without* a route
..    name.) The view is called with the exception object as its *context*, and
..    whatever response the view returns will be sent to the browser. You can thus
..    use an exception view to customize the error screen shown to the user.

2. リクエストの処理中に捕捉されない例外が生じた場合、 Pyramid はそれを
   捕捉して、その例外とマッチする「例外ビュー」を探します。例外ビューとは、
   それに対する *context* 引数がその例外クラスや祖先、あるいはその例外が
   実装しているインターフェースであるようなものです。さらに他のすべての
   ビュー述語もマッチしなければなりません; 例えば 'route_name' 引数が
   指定されている場合、それは実際の route 名とマッチしなければなりません
   (したがって、例外ビューは典型的に route 名 *なしで* 登録されます)。
   このビューは、例外オブジェクトが *context* として呼ばれます。そして、
   ビューが返した任意のレスポンスがブラウザに送られます。このように、
   ユーザに見せるエラー画面をカスタマイズするために例外ビューを使用する
   ことができます。


.. 3. If no matching exception view is found, HTTP exceptions are their own
..    response so they are sent to the browser. Standard HTTPExceptions have a
..    simple error message and layout; subclasses can customize this.

3. マッチする例外ビューが見つからない場合、 HTTP 例外はそれ自体がレスポンス
   になります。したがって、それらはブラウザに送られます。標準
   HTTPException は単純なエラーメッセージとレイアウトを持っています;
   サブクラスはこれをカスタマイズすることができます。


.. 4. Non-HTTPException responses propagate to the WSGI server. If the debug
..    toolbar tween is enabled, it will catch the exception and display the
..    interactive traceback. Otherwise the WSGI server will catch it and send its
..    own "500 Internal Server Error" screen.

4. HTTPException 以外のレスポンスは WSGI サーバーに伝搬します。
   debug ツールバー tween が有効であれば、それは例外を捕捉して対話型の
   トレースバックを表示します。そうでなければ、 WSGI サーバが例外を捕捉して
   それ自身の "500 Internal Server Error" 画面を送信します。


.. Here are the most popular HTTP exceptions:

これらは、代表的な HTTP 例外です:


.. =======================  ====  ========  ====================================== 
.. Class                    Code  Location  Meaning
.. =======================  ====  ========  ====================================== 
.. HTTPMovedPermanently      301  Y         Permanent redirect; client should 
..                                          change bookmarks.
.. HTTPFound                 302  Y         Temporary redirect. [1]_
.. HTTPSeeOther              303  Y         Temporary redirect; client should use
..                                          GET. [1]_
.. HTTPTemporaryRedirect     307  Y         Temporary redirect. [1]_
.. HTTPClientError           400  N         General user error; e.g., invalid 
..                                          query param.
.. HTTPUnauthorized          401  N         User must authenticate.
.. HTTPForbidden             403  N         Authorization failure, or general 
..                                          refusal.
.. HTTPNotFound              404  N         The URL is not recognized.
.. HTTPGone                  410  N         The resource formerly at this URL is 
..                                          permanently gone; client should delete
..                                          bookmarks.
.. HTTPInternalServerError   500  N         The server could not process the 
..                                          request due to an internal error.
.. =======================  ====  ========  ====================================== 

=======================  ======  ========  ======================================== 
クラス                   コード  Location  意味
=======================  ======  ========  ========================================
HTTPMovedPermanently      301    Y         恒久的なリダイレクト; クライアントは
                                           ブックマークを変更するべき。
HTTPFound                 302    Y         一時的なリダイレクト。 [1]_
HTTPSeeOther              303    Y         一時的なリダイレクト; クライアントは
                                           GET を使うべき。 [1]_
HTTPTemporaryRedirect     307    Y         一時的なリダイレクト。 [1]_
HTTPClientError           400    N         一般的なユーザエラー; 例えば、不正な
                                           クエリパラメータ。
HTTPUnauthorized          401    N         ユーザは認証が必要。
HTTPForbidden             403    N         認証の失敗、または一般的な拒否。
HTTPNotFound              404    N         この URL は認識されない。
HTTPGone                  410    N         以前この URL に存在したリソースは
                                           恒久的に利用できなくなった;
                                           クライアントはブックマークを削除すべき。
HTTPInternalServerError   500    N         内部エラーによりサーバーはリクエストを
                                           処理することができなかった。
=======================  ======  ========  ========================================


.. The constructor args for classes with a "Y" in the location column are
.. ``(location="", detail=None, headers=None, comment=None, ...)``. Otherwise the
.. constructor args are ``(detail=None, headers=None, comment=None, ...)``.

location カラムが "Y" のクラスのコンストラクタ引数は ``(location="",
detail=None, headers=None, comment=None, ...)`` です。それ以外は、
コンストラクタ引数は ``(detail=None, headers=None, comment=None, ...)``
です。


.. The ``location`` argument is optional at the Python level, but the HTTP spec 
.. requires a location that's an absolute URL, so it's effectively required.

``location`` 引数は Python レベルではオプションですが、 HTTP 仕様は
絶対 URL である位置を要求しています。したがって、それは事実上必須です。


.. The ``detail`` argument may be a plain-text string which will be incorporated
.. into the error screen. ``headers`` may be a list of HTTP headers (name-value
.. tuples) to add to the response. ``comment`` may be a plain-text string which is
.. not shown to the user. (XXX Is it logged?)

``detail`` 引数はエラー画面の一部として表示されるプレーンテキスト文字列です。
``headers`` はレスポンスに加えたい HTTP ヘッダー (name-value タプル) の
リストです。 ``comment`` はユーザには表示されないプレーンテキスト文字列です。
(XXX これはログに記録される?)


.. Exception views

例外ビュー
===============

.. You can register an exception view for any exception class, although it's most
.. commonly used with ``HTTPNotFound`` or ``HTTPForbidden``.  Here's an example of
.. an exception view with a custom exception, borrowed from the Pyramid manual:

例外ビューは ``HTTPNotFound`` または ``HTTPForbidden`` と一緒に使用される
ことが最も普通ですが、任意の例外クラスに対して例外ビューを登録することが
できます。これは、 Pyramid マニュアルから拝借した、カスタム例外を用いた
例外ビューの例です:


::

    from pyramid.response import Response

    class ValidationFailure(Exception):
        pass

    @view_config(context=ValidationFailure)
    def failed_validation(exc, request):
        # If the view has two formal arguments, the first is the context.
        # The context is always available as ``request.context`` too.
        msg = exc.args[0] if exc.args else ""
        response =  Response('Failed validation: %s' % msg)
    response.status_int = 500
    return response


.. For convenience, Pyramid has special decorators and configurator methods to
.. register a "Not Found" view or a "Forbidden" view. ``@notfound_view_config``
.. and ``@forbidden_view_config`` (defined in ``pyramid.view``) takes care of the
.. context argument for you.

利便性のため、 Pyramid は "Not Found" ビューや "Forbidden" ビューを登録
するための特別なデコレータおよび Configurator メソッドを持っています。
``@notfound_view_config`` と ``@forbidden_view_config``
(``pyramid.view`` で定義) は、コンテキスト引数の面倒を見ます。


.. Additionally, ``@notfound_view_config`` accepts an ``append_slash`` argument,
.. which can be used to enforce a trailing-slash convention. If your site defines
.. all its routes to end in a slash and you set ``append_slash=True``, then when
.. a slashless request doesn't match any route, Pyramid try again with a slash
.. appended to the request URL. If *that* matches a route, Pyramid will issue a
.. redirect to it. This is useful only for sites that prefer a trailing slash
.. ("/dir/" and "/dir/a/"). Other sites prefer *not* to have a trailing slash
.. ("/dir" and "/dir/a"), and there are no special features for this.

加えて、 ``@notfound_view_config`` は ``append_slash`` 引数を受け付けます。
それは末尾のスラッシュ規約を強制するために使用できます。あなたのサイトが
すべての route はスラッシュで終るべきと定義していて、
``append_slash=True`` をセットした場合、スラッシュのないリクエストが
route とマッチしなかった場合、Pyramid はリクエスト URL にスラッシュを
追加して再びマッチを試みます。これが route とマッチすれば、 Pyramid は
リダイレクトを実行します。これは、末尾のスラッシュ ("/dir/" や
"/dir/a/") を好むサイトでのみ役立ちます。他のサイトでは末尾のスラッシュ
を好まれず ("/dir" and "/dir/a") 、またこのための特別な機能はありません。


.. Reference

参考
=========

* `HTTP exceptions <http://docs.pylonsproject.org/projects/pyramid/en/latest/api/httpexceptions.html>`_
* `HTTP exception usage and exception views <http://docs.pylonsproject.org/projects/pyramid/en/latest/narr/views.html#using-special-exceptions-in-view-callables>`_


.. .. [1] The three temporary redirect statuses are largely interchangeable
..    but have slightly different purposes. Details in the HTTP status
..    reference.

.. [1] 3つの一時的なリダイレクトステータスは、大部分は交換可能ですが、
   わずかに異なる目的があります。詳細は HTTP ステータスのリファレンスに
   あります。
