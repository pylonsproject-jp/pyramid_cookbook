.. Templates

テンプレート
++++++++++++

.. Pyramid includes adapters for two template engines, Mako and Chameleon. Mako is
.. Pylons' default engine so it will be familiar. Third-party adapters are
.. available for other engines: "pyramid_jinja2" (a Jinja2 adapter),
.. "pyramid_chameleon_gensi" (a partial Genshi emulator), etc. 

Pyramid は、 2 つのテンプレートエンジン Mako および Chameleon の
アダプターを含んでいます。 Mako は Pylons のデフォルトエンジンなので
馴染みがあるでしょう。他のエンジンに対するサードパーティのアダプターが
利用可能です: "pyramid_jinja2" (Jinja2 アダプター)、
"pyramid_chameleon_gensi" (部分的な Genshi エミュレーター) など。


.. Mako configuration

Mako 設定
==================

.. In order to use Mako as in Pylons, you must specify a template search path
.. in the settings:

Pylons で Mako を使用するために、設定の中でテンプレート検索パスを
指定しなければなりません:


.. code-block:: ini

   [app:main]
   ...
   mako.directories = pyramidapp:templates


.. This enables relative template paths like ``renderer="/mytemplate.mak"`` and
.. quasi-URL paths like ``renderer="/mytemplate.mak"``. It also allows templates
.. to inherit from other templates, import other templates, and include other
.. templates. Without this setting, the renderer arg will have to be in asset
.. spec syntax, and templates won't be able to invoke other templates. 

これによって ``renderer="/mytemplate.mak"`` のような相対的なテンプレート
パスや ``renderer="/mytemplate.mak"`` のような疑似 URL パスが使えるように
なります。さらに、これによってテンプレートが他のテンプレートから継承したり、
他のテンプレートをインポートしたり、他のテンプレートをインクルードしたり
することが可能になります。この設定なしでは、レンダラー引数は asset
スペック構文でなければならず、テンプレートは他のテンプレートを起動する
ことができないでしょう。


.. All settings with the "mako." prefix are passed to Mako's ``TemplateLookup``
.. constructor. E.g., 

"mako." 接頭辞を持つすべての設定は Mako の ``TemplateLookup``
コンストラクタに渡されます。例えば、


.. code-block:: ini

   mako.strict_undefined = true
   mako.imports = 
        from mypackage import myfilter
   mako.filters = myfilter
   mako.module_directory = %(here)s/data/templates
   mako.preprocessor = mypackage.mako_preprocessor


.. Template filenames ending in ".mak" or ".mako" are sent to the Mako renderer.
.. If you prefer a different extension such as ".html", you can put this
.. in your main function:

".mak" または ".mako" で終わるテンプレートファイル名は Mako レンダラー
に送られます。 ".html" のような他の拡張子が使いたければ、 main 関数に
これを入れてください:


::

    config.add_renderer(".html", "pyramid.mako_templating.renderer_factory")

    
.. If you have further questions about exactly how the Mako renderer is
.. implemented, it's best to look at the source: ``pyramid.mako_templating``. You
.. can reconcile that with the Mako documentation to confirm what argument values
.. cause what.

Mako レンダラーが正確にどのように実装されているかに関してさらなる質問が
あれば、ソースを見るのが一番です: ``pyramid.mako_templating`` 。
どの引数の値が何を引き起こすかを確認するために、ソースコードと Mako
ドキュメンテーションを照合することができます。


.. *Caution:* When I set "mako.strict_undefined" to true in an application that
.. didn't have Beacon sessons configured, it broke the debug toolbar. The toolbar
.. templates may have some sloppy placeholders not guarded by "% if".

*注意:* Beacon sessons (訳注: Beaker session のこと?) を設定していない
アプリケーションで "mako.strict_undefined" を true にセットしたら、
それは debug ツールバーを壊しました。ツールバーテンプレートには
"% if" によって保護されていない sloppy (不注意、だらしない) な
プレースホルダーがいくつかあるのかもしれません。


.. *Caution 2:* Supposedly you can pass an asset spec instead of a template path
.. but I couldn't get it to work.

*注意2:* テンプレートパスの代わりに asset スペックを渡すことができる
はずですが、私はそれを動作させることができませんでした。


Chameleon
=========

.. Chameleon is an XML-based template language descended from Zope. It has some
.. similarities with Genshi. Its filename extension is .pt ("page template").  

Chameleon は Zope に由来する XML ベースのテンプレート言語です。
それには Genshi とのいくつかの類似点があります。そのファイル名拡張子
は .pt (「ページテンプレート」) です。


.. Advantages of Chameleon:

Chameleon の利点:


.. * XML-based syntax.
.. * Template must be well-formed XHTML, suggesting (but not guaranteeing) that the
..   output will be well-formed. If any variable placeholder is marked
..   "structure", it's possible to insert invalid XML into the template.
.. * Good internationalization support in Pyramid.
.. * Speed is as fast as Mako. (Unusual for XML template languages.)
.. * Placeholder syntax "${varname or expression}" is common to Chameleon, Mako,
..   and Genshi.
.. * Chameleon does have a text mode which accepts non-XML input, but you lose all
..   control structures except "${varname}".

* XML ベースの構文。
* テンプレートは well-formed な XHTML でなければなりません。これは出力
  が well-formed であることを示唆します (ただし保証はされません)。
  いずれかの変数プレースホルダーが "structure" とマークされる場合、
  テンプレートに無効な XML を挿入することが可能です。
* Pyramid の中で国際化サポートがしっかりしています。
* 速度は Mako と同じくらい高速です (XML テンプレート言語にも関わらず)。
* プレースホルダー構文 "${varname or expression}" は Chameleon, Mako,
  Genshi で共通です。
* Chameleon には非 XML 入力を受け付けるテキストモードがあります。
  ただし、 "${varname}" を除き制御構造はすべて失われます。


.. Disadvantages of Chameleon:

Chameleon の欠点:


.. * XML-based syntax.
.. * Filenames must be in asset spec syntax, not relative paths:
..   ``renderer="mypackage:templates/foo.pt"``, ``renderer="templates/foo.pt"``.
..   You can't get rid of that "templates/" prefix without writing a wrapper
..   ``view_config`` decorator.
.. * No template lookup, so you can't invoke one template from inside another
..   without pre-loading the template into a variable.
.. * If template is not well-formed XML, the user will get an unconditional
..   "Internal Server Error" rather than something that might look fine in the
..   browser and which the user can at least read some content from.
.. * It doesn't work on all platforms Mako and Pyramid do. (Only CPython and
..   Google App Engine.)

* XML ベースの構文。
* ファイル名は相対パスではなく asset スペック構文でなければなりません:
  ``renderer="mypackage:templates/foo.pt"``, ``renderer="templates/foo.pt"`` 。
  ラッパー ``view_config`` デコレータを書かない限り "templates/" 接頭辞を
  省略することはできません。
* テンプレートのルックアップはありません。したがって、テンプレートを
  あらかじめ変数にロードしておかない限り、あるテンプレートを別の
  テンプレートの内部から起動することはできません。
* テンプレートが well-formed な XML でなければ、見た目の良い、少なくとも
  ユーザが何らかの内容を読むことができるページがブラウザに表示される
  代わりに、ユーザは無条件に「内部サーバーエラー」を得るでしょう。
* Mako と Pyramid が動作するすべてのプラットフォームでは動作しません
  (CPython および Google App Engine のみ)。


.. Renderer globals

レンダラーグローバル変数
========================

.. Whenever a renderer invokes a template, the template namespace includes all the
.. variables in the view's return dict, plus the following system variables:

レンダラーがテンプレートを起動する場合は常に、テンプレートネームスペース
にはビューが返した辞書に加えて以下のシステム変数のすべての変数が含まれて
います:


.. attribute:: request, req
   :noindex:

   .. The current request.

   現在のリクエスト。


.. attribute:: view

   .. The view instance (for class-based views) or function (for function-based
   .. views). You can read instance attributes directly:  ``view.foo``.

   ビューインスタンス (クラスベースのビューの場合) または関数
   (関数ベースのビューの場合)。インスタンス属性を直接読むことができます:
   ``view.foo`` 。


.. attribute:: context
   :noindex:

   .. The context (same as ``request.context``).  (Not visible in Mako because
   .. Mako has a built-in variable with this name; use ``request.context``
   .. instead.)

   コンテキスト (``request.context`` と同じ)。 (Mako にはこの名前を
   持った内蔵の変数があるので Mako において可視ではありません;
   代わりに ``request.context`` を使用します。)


.. attribute:: renderer_name
   :noindex:

   .. The fully-qualified renderer name; e.g., "zzz:templates/foo.mako".

   完全修飾されたレンダラー名。たとえば　"zzz:templates/foo.mako" 。


.. attribute:: renderer_info
   :noindex:

   .. An object with attributes ``name``, ``package``, and ``type``.

   属性 ``name``, ``package``, ``type`` を持つオブジェクト。



.. The Akhet demo shows how to inject other variables into all templates, such as
.. a helpers module ``h``, a URL generator ``url``,  the session variable
.. ``session``, etc.

Akhet デモは、 helpers モジュール ``h`` 、URL ジェネレーター ``url`` 、
セッション変数 ``session`` などのような他の変数をすべてのテンプレートに
注入する方法を示しています。


.. Site template

サイトテンプレート
==================

.. Most sites will use a site template combined with page templates to ensure
.. that all the pages have the same look and feel (header, sidebars, and footer).
.. Mako's inheritance makes it easy to make page templates inherit from a site
.. template. Here's a very simple site template:

ほとんどのサイトは、すべてのページが同じルックアンドフィール (ヘッダー、
サイドバー、フッター) を持つことを保証するためにページテンプレートと
結合したサイトテンプレートを使用するでしょう。Mako の継承は、サイト
テンプレートからページテンプレートを継承することを簡単にします。これは
非常に単純なサイトテンプレートです:


.. code-block::  mako

    <!DOCTYPE html>
    <html>
      <head>
        <title>My Application</title>
      </head>
      <body>

    <!-- *** BEGIN page content *** -->
    ${self.body()}
    <!-- *** END page content ***-->

      </body>
    </html>


.. ... and a page template that uses it:

... また、それを使用するページテンプレートは:



.. code-block:: mako

    <%inherit file="/site.html" />

    <p>
      Welcome to <strong>${project}</strong>, an application ...
    </p>


.. A more elaborate example is in the Akhet demo.

より精巧な例が Akhet デモの中にあります。
