.. Route and View Examples

route とビューのサンプル
++++++++++++++++++++++++

.. Here are the most common kinds of routes and views.

これは、最も一般的な種類の route とビューです。


.. 1. Fixed controller and action.

1. 固定のコントローラーとアクション。


   ::

        # Pylons
        map.connect("faq", "/help/faq", controller="help", action="faq")
        class HelpController(Base):
            def faq(self):
                ...

   ::

        # Pyramid
        config.add_route("faq", "/help/faq")
        @view_config(route_name="faq", renderer="...")
        def faq(self):   # In some arbitrary class.
            ...

   .


.. 2. Fixed  controller and action, plus other routing variables.

2. 固定のコントローラとアクション、加えて他のルーティング変数。


   ::

        # Pylons
        map.connect("article", "/article/{id}", controller="foo",
            action="article")
        class FooController(Base):
            def article(self, id):
                ...

   ::

        # Pyramid
        config.add_route("article", "/article/{id}")
        @view_config(route_name="article")
        def article(self):   # In some arbitrary class.
            id = self.request.matchdict["id"]


   .


.. 3. Variable controller and action.

3. 可変のコントローラとアクション。


   ::

        # Pylons
        map.connect("/{controller}/{action}")
        map.connect("/{controller/{action}/{id}")

   ::

        # Pyramid
        # Not possible.


   .. You can't choose a view class via a routing variable in Pyramid.

   Pyramid ではルーティング変数によってビュークラスを選択することは
   できません。


.. 4. Fixed controller, variable action.

4. 固定のコントローラ、可変のアクション。


   ::

        # Pylons
        map.connect("help", "/help/{action}", controller="help")

   ::

        # Pyramid
        config.add_route("help", "/help/{action}")

        @view_config(route_name="help", match_param="action=help", ...)
        def help(self):   # In some arbitrary class.
            ...


   .. The 'pyramid_handlers' package provides an alternative for this.

   'pyramid_handlers' パッケージはこれとは別の選択肢を提供します。



.. Other Pyramid examples:

他の Pyramid の例:


::

    # Home route.
    config.add_route("home", "/")

    # Multi-action route, excluding certain static URLs.
    config.add_route("main", "/{action}",
        path_info=r"/(?!favicon\.ico|robots\.txt|w3c)")



pyramid_handlers
================

.. "pyramid_handlers_" is an add-on package that provides a possibly more
.. convenient way to handle case #4 above, a route with an 'action' variable
.. naming a view. It works like this:

"pyramid_handlers_" は、上記のケース #4 を扱うためのおそらくより便利な
方法 (ビューを指定する 'action' 変数を含んだ route) を提供するアドオン
パッケージです。それはこのように作動します:


.. literalinclude:: code/pyramid_handlers.py
   :linenos:


.. The ``add_handler`` method (line 6) registers the route and then scans the
.. Hello class.  It registers as views all methods that have an ``@action``
.. decorator, using the method name as a view predicate, so that when that method
.. name appears in the 'action' part of the URL, Pyramid calls this view.

``add_handler`` メソッド (6行目) は、 route を登録し、次に Hello クラス
を走査します。それは ``@action`` デコレータを持つすべてのメソッドを、
メソッド名をビュー述語として使用してビューとして登録します。その結果、
メソッド名が URL の 'action' 部分に現われた場合、 Pyramid はこのビュー
を呼び出します。


.. The ``__autoexpose__`` class attribute (line 11) can be a regex. If any method
.. name matches it, it will be registered as a view even if it doesn't have an
.. ``@action`` decorator. The default autoexpose regex matches all methods that
.. begin with a letter, so you'll have to set it to None to prevent methods from
.. being automatically exposed. You can do this in a base class if you wish.

``__autoexpose__`` クラス属性 (11行目) は正規表現にすることも可能です。
任意のメソッド名がそれと一致すれば、 ``@action`` デコレータがなくても
ビューとして登録されます。デフォルトの自動 expose 正規表現は英文字から
始まるすべてのメソッドとマッチします。したがって、メソッドが自動的に
expose されるのを避けるためには、それを None に設定しなければなりません。
もしそうしたければ、ベースクラスの中でこれをすることができます。


.. Note that ``@action`` decorators are *not* recognized by ``config.scan()``. 
.. They work only with ``config.add_hander``.

``@action`` デコレータは ``config.scan()`` によって認識 *されない* ことに
注意してください。 ``@action`` デコレータは ``config.add_hander``
でのみ動作します。


.. User reaction to "pyramid_handlers" has been mixed. A few people are using it,
.. but most people use ``@view_config`` because it's "standard Pyramid".

"pyramid_handlers" に対するユーザの反応は様々です。一部のユーザはそれを
使っています。しかし、ほとんどのユーザはそれが「標準の Pyramid 」だから
という理由で ``@view_config`` を使っています。


.. Resouce routes

リソース route
==============

.. "pyramid_routehelper_" provides a ``config.add_resource`` method that behaves
.. like Pylons' ``map.resource``. It adds a suite of routes to
.. list/view/add/modify/delete a resource in a RESTful manner (following the Atom
.. publishing protocol).  See the source docstrings in the link for details.

"pyramid_routehelper_" は、 Pylons の ``map.resource`` のように動作する
``config.add_resource`` メソッドを提供します。それは、 RESTful なやり方
で (Atom publishing protocol に従って) リソースを一覧/表示/追加/修正/削
除する route 一式を加えます。詳細については、リンク先のソースコードの
docstring を参照してください。


.. Note: the word "resource" here is *not* related to traversal resources.

注: ここでの単語「リソース」はトラバーサルのリソースとは関係 *ありません* 。


.. include::  ../links.rst
