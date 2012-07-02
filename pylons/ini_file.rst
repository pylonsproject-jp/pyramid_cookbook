.. INI File

INI ファイル
++++++++++++

.. The "[app:main]" section in Pyramid apps has different options than its Pylons
.. counterpart. Here's what it looks like in Pyramid's "alchemy" scaffold:

Pyramid アプリケーション中の "[app:main]" セクションには、 Pylons におけ
る同等のアプリケーションとは異なるオプションがあります。以下に
Pyramid の "alchemy" scaffold で "[app:main]" セクションがどうなっているか
を示します:


.. code-block:: ini

    [app:main]
    use = egg:{{project}}

    pyramid.reload_templates = true
    pyramid.debug_authorization = false
    pyramid.debug_notfound = false
    pyramid.debug_routematch = false
    pyramid.debug_templates = true
    pyramid.default_locale_name = en
    pyramid.includes =
        pyramid_debugtoolbar
        pyramid_tm

    sqlalchemy.url = sqlite:///%(here)s/{{project}}.db


.. The "pyramid.includes=" variable lists a number of "tweens" to activate. A
.. tween is like a WSGI middleware but specific to Pyramid.  "pyramid_debugtoolbar"
.. is the debug toolbar; it provides information on the request variables and
.. runtime state on every page.

"pyramid.includes=" 変数は、有効にしようとするいくつかの "tween" が
列挙されています。tween は WSGI ミドルウェアに似ていますが、 Pyramid に
特有のものです。"pyramid_debugtoolbar" はデバッグツールバーです; それは、
すべてのページのリクエスト変数およびランタイム状態についての情報を提供
します。


.. "pyramid_tm" is a transaction manager. This has no equivalent in Pylons but is
.. used in TurboGears and BFG. It provides a request-wide transaction that manages
.. your SQLAlchemy session(s) and potentially other kinds of transactions like
.. email sending. This means you don't have to call ``DBSession.commit()`` in your
.. view. At the end of the request, it will automatically commit the database
.. session(s) and send any pending emails, unless an uncaught exception was raised
.. during the session, in which case it will roll them back. It has functions to
.. allow you to commit or roll back the request-wide transaction at any time, or
.. to "doom" it to prevent any other code from committing anything.

"pyramid_tm" はトランザクションマネージャです。 Pylons にはこれに相当
するものはありませんが、 TurboGears と BFG の中で使用されています。
pyramid_tm は、 SQLAlchemy セッション、あるいは電子メール送信のような
潜在的に別の種類のトランザクションを管理するための、リクエスト全体に
渡るトランザクションを提供します。これは、ビューの中で
``DBSession.commit()`` を呼ぶ必要はないということを意味します。それは
リクエストの終わりに自動的にデータベースセッションをコミットし、すべて
の未送信の電子メールも送るでしょう。ただしセッション中で uncaught 例外が
上げられた場合、トランザクションはロールバックされます。それはリクエスト
全体に渡るトランザクションが常にコミットかロールバックかのいずれかに
なるようにする、あるいは他のあらゆるコードが何かをコミットすることを防ぐ、
という機能を持っています。


.. The other "pyramid.\*" options are for debugging. Set any of these
.. to true to tell that subsystem to log what it's
.. doing. The messages will be logged at the DEBUG level. (The reason these aren't
.. in the logging configuration in the bottom part of the INI file is that they
.. were established early in Pyramid's history before it had adopted INI-style
.. logging configuration.)


他の "pyramid.\*" オプションは、デバッグ用です。サブシステムが行っている
ことをログに記録するためには、これらを true に設定してください。メッセージ
は DEBUG レベルでログに記録されるでしょう (これらが INI ファイル下部の
logging 設定にない理由は、 INI スタイルの logging 設定が採用される以前に
Pyramid の歴史の初期に確立されたためです)。


.. If "pyramid.reload_templates=true", the template engine will check the
.. timestamp of the template source file every time it renders a template, and
.. recompile the template if its source has changed. This works only for template
.. engines and Pyramid-template adapaters that support this feature.  Mako and
.. Chameleon do.

"pyramid.reload_templates=true" の場合、テンプレートエンジンはテンプレート
をレンダリングする度にテンプレートソースファイルのタイムスタンプを
チェックし、もしそのソースが変わっていればテンプレートを再コンパイルし
ます。これは、この機能をサポートするテンプレートエンジンおよび Pyramid
テンプレートアダプタでのみ機能します。 Mako と Chameleon はこれを行います。


.. The "sqlalchemy.url=" line is for SQLAlchemy.  "%(here)s" expands to the path
.. of the directory containing the INI file. You can add settings for any library
.. that understands them, including SQLAlchemy, Mako, and Beaker. You can also
.. define custom settings that your application code understands, so that you can
.. deploy it with different configurations without changing the code. This is all
.. the same as in Pylons.

"sqlalchemy.url=" という行は SQLAlchemy 用です。 "%(here)s" は INI
ファイルを含むディレクトリのパスに展開されます。 SQLAlchemy, Mako,
Beaker を含め、それらを理解するあらゆるライブラリのための設定を加える
ことができます。さらに、アプリケーションコードが理解するカスタム設定を
定義することができます。その結果、コードを変更せずに、異なる設定でそれ
をデプロイすることができます。これは Pylons の場合とまったく同様です。


.. *production.ini* has the same app settings as *development.ini*, except that
.. the "pyramid_debugtoolbar" tween is *not* present, and all the debug settings
.. are false. The debug toolbar *must* be disabled in production because it's a
.. potential security hole: anybody who can force an exception and get an
.. interactive traceback can run arbitrary Python commmands in the application
.. process, and thus read or modify files or execute programs.  So never enable
.. the debug toolbar when the site is accessible on the Internet, except perhaps
.. in a wide-area development scenario where higher-level access restrictions
.. (Apache) allow only trusted developers and beta testers to get to the site.

*production.ini* は、 "pyramid_debugtoolbar" tween が *存在しない* 以外
は *development.ini* と同じ app 設定を持っています。また、デバッグ設定は
すべて false です。デバッグツールバーは潜在的なセキュリティホールなので
プロダクション環境では無効に *しなければなりません*: 強制的に例外を
起こして対話型のトレースバックを見ることができれば誰でもアプリケーション
プロセス内で任意の Python コマンドを実行することができ、したがってファイル
を読んだり修正したりプログラムを実行したりすることができます。そのため、
サイトがインターネット上でアクセス可能な場合、デバッグツールバーを有効
にしないでください。例外は、おそらくより高レベル (Apache) のアクセス制限
によって信頼できる開発者とベータテスタだけがサイトへのアクセスを許可
されるような広域開発 (wide-area development) シナリオでしょう。


.. Pyramid no longer uses WSGI middleware by default. In most cases you can find a
.. tween or Pyramid add-on package that does the equivalent. If you need to
.. activate your own middleware, do it the same way as in Pylons; the syntax is in
.. the `PasteDeploy manual`_. But first consider whether making a Pyramid tween
.. would be just as convenient. Tweens have a much simpler API than middleware,
.. and have access to the view's request and response objects. The WSGI protocol is
.. extraordinarily difficult to implement correctly due to edge cases, and many
.. existing middlewares are incorrect. Let server developers and framework
.. developers worry about those issues; you can just write a tween and be out on the
.. golf course by 3pm.

Pyramid は、もはやデフォルトで WSGI ミドルウェアを使用しません。ほとんど
の場合、等価な処理を行う tween または Pyramid アドオンパッケージを見つける
ことができます。自分のミドルウェアを有効化する必要がある場合は、 Pylons
の場合と同じ方法で行ってください; その文法は `PasteDeploy manual`_ に
あります。しかし、 Pyramid tween を作成することが同じくらい便利ではない
かと最初に考えてください。 tween はミドルウェアよりはるかに単純な API
を持っており、ビューの request および response オブジェクトにアクセスで
きます。 WSGI プロトコルは、エッジケースのために正確に実装することは
非常に困難で、また多くの既存のミドルウェアは不正確です。それらの問題に
ついてサーバー開発者およびフレームワーク開発者を心配させてください;
単に tween を書き、午後 3 時までにゴルフ場に出かけることができます。


.. _PasteDeploy manual: http://pythonpaste.org/deploy/
