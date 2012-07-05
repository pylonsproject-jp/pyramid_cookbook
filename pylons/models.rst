.. Models

モデル
++++++

.. Models are essentially the same in Pyramid and Pylons because the framework
.. is only minimally involved with the model unlike, say, Django where the ORM
.. (object-relational mapper) is framework-specific and other parts of the
.. framework assume it's that specific kind. In Pyramid and Pylons, the
.. application skeleton merely suggests where to put the models and initializes a
.. SQLAlchemy database connection for you. Here's the
.. default Pyramid configuration (comments stripped and imports squashed):

フレームワークはモデルとは最小限にしか関連しないので、 Pyramid と
Pylons でモデルは本質的に同じです (これは、例えば Django とは異なります。
Django は ORM (オブジェクト・リレーショナル・マッパー) がフレームワーク
に特有で、フレームワークの他の部分はそれが特定の種類であると仮定します)。
Pyramid と Pylons では、アプリケーションスケルトンは単にどこにモデルを
置くべきかを示唆し、 SQLAlchemy データベース接続を初期化します。以下は、
デフォルトの Pyramid 設定です (コメントは除いて、インポートは省略しています):


.. literalinclude:: code/models.py
   :linenos:


.. and its INI files:

そして INI ファイルは:


.. literalinclude:: code/model_development.ini
   :linenos:


.. It has the following differences from Pylons:

それは Pylons と次のような違いがあります:


.. 1. ZopeTransactionExtension and the "pyramid_tm" tween.
.. 2. "models" (plural) instead of "model" (singular).
.. 3. A module rather than a subpackage.
.. 4. "DBSession" instead of "Session".
.. 5. No init_model() function.
.. 6. Slightly different import style and variable naming.

1. ZopeTransactionExtension および "pyramid_tm" tween 。
2. "model" (単数) の代わりに "models" (複数)。
3. サブパッケージではなくモジュール。
4. "Session" の代わりに "DBSession" 。
5. init_model() 関数はありません。
6. わずかに異なるインポートスタイルおよび変数の命名規則。


.. Only the first one is an essential difference; the rest are just aesthetic
.. programming styles.  So you can change them without harming anything.

最初のものだけが本質的な違いです; 残りは単に審美的なプログラミング
スタイルです。したがって、何も傷つけることなくそれらを変更することができます。


.. The model-models difference is due to an ambiguity in how the word "model" is
.. used. Some people say "a model" to refer to an individual ORM class, while
.. others say "the model" to refer to the entire collection of ORM classes in an
.. application. This guide uses the word both ways.

model と models の違いは、単語「モデル」の使われ方の曖昧性によります。
ある人々は、個々の ORM クラスを指して "a model" と呼び、その一方で他の
人々は、アプリケーション内の ORM クラスの全コレクションを指して "the
model" と呼びます。このガイドでは単語「モデル」を両方の意味で使用します。


.. What belongs in the model?

何がモデルに属しているか
==========================

.. Good programming practice recommends keeping your data classes separate from
.. user-interface classes. That way the user interface can change without
.. affecting the data and vice-versa. The model is where the data classes go.
.. For instance, a Monopoly game has players, a board, squares, title deeds,
.. cards, etc, so a Monopoly program would likely have classes for each of these.
.. If the application requires saving data between runs (persistence), the data
.. will have to be stored in a database or equivalent. Pyramid can work with a
.. variety of database types: SQL database, object database, key-value database
.. ("NoSQL"), document database (JSON or XML), CSV files, etc. The most common
.. choice is SQLAlchemy, so that's the first configuration provided by Pyramid and
.. Pylons.

良いプログラミングプラクティスは、データクラスとユーザインタフェース
クラスを分離しておくことを推奨します。このように、ユーザインタフェース
はデータに影響せずに変わることがあり、その逆もあります。モデルはデータ
クラスの行き先です。例えば、モノポリーゲームにはプレーヤー、ボード、
ゲーム盤、不動産権利書、カードなどがあります。したがって、モノポリー
プログラムは、おそらくこれら各々のためのクラスを持つでしょう。
アプリケーションが実行間でのデータの保存 (永続化) を必要とすれば、
データはデータベースまたはそれに相当するものに保存されなければならない
でしょう。 Pyramid は様々なデータベースタイプで動作することができます:
SQL データベース、オブジェクトデータベース、 Key-Value データベース
("NoSQL")、ドキュメントデータベース (JSON や XML)、 CSV ファイルなど。
最も一般的な選択は SQLAlchemy です。したがって、それは Pyramid と
Pylons によって提供される最初の構成です。


.. At minimum you should define your ORM classes in the model. You can also add
.. any business logic in the form of functions, class methods, or regular methods.
.. It's sometimes difficult to tell whether a particular piece of code belongs in
.. the model or the view, but we'll leave that up to you. 

最低限、モデルに ORM クラスを定義する必要があります。さらに、関数、
クラスメソッドあるいはクラスメソッドの形で任意のビジネスロジックを
加えることもできます。特定のコード片がモデルに属するかビューに属するかを
伝えることは時に困難ですが、それはあなたに任せます。


.. Another principle is that the model should not depend on the rest of the
.. application so that it can be used on its own; e.g., in utility programs or in
.. other applications. That also allows you to extract the data if the framework
.. or application breaks. So the view knows about the model but not vice-versa. Not
.. everybody agrees with this but it's a good place to start.

別の原理は、モデル単独で使用できるように、モデルはアプリケーションの
残りに依存すべきでないということです; 例えば、ユーティリティープログラム、
あるいは他のアプリケーションの中で。またそれによって、フレームワークまたは
アプリケーションが壊れた時にデータを抽出することが可能になります。
したがって、ビューはモデルのことを知っていますが、逆は真ではありません。
必ずしも誰もがこれに賛成するとは限りませんが、手始めとしては良い考えです。


.. Larger projects may share a common model between multiple web applications and
.. non-web programs. In that case it makes sense to put the model in a separate
.. top-level package and import it into the Pyramid application.

より大きなプロジェクトは、多数のウェブアプリケーションと非ウェブ
プログラムの間の統一モデルを共有するかもしれません。その場合、個別の
トップレベルのパッケージにモデルを入れて、 Pyramid アプリケーションへ
それをインポートすることは意味のあることです。


.. Transaction manger

トランザクションマネージャ
==========================

.. Pylons has never used a transaction manager but it's common in TurboGears and
.. Zope. A transaction manager takes care of the commit-rollback cycle for you.
.. The database session in both applications above is a *scoped* session, meaning
.. it's a threadlocal global and must be cleared out at the end of every request.
.. The Pylons app has special code in the base controller to clear out the
.. session. A transaction manager takes this a step further by committing any
.. changes made during the request, or if an exception was raised during the
.. request, it rolls back the changes. The ZopeTransactionExtension provides a
.. module-level API in case the view wants to customize when/whether committing
.. occurs.

Pylons はトランザクションマネージャを使用していませんが、 TurboGears と
Zope においてそれは一般的です。トランザクションマネージャはあなたのために
コミット- ロールバック・サイクルの面倒を見ます。上記の両方のアプリケーション
でのデータベースセッションは *スコープ* セッションです。その意味は、
それは threadlocal グローバル変数で、すべてのリクエストの終わりにそれを
空にしなければならないということです。Pylons アプリでは、ベースコントローラ
の中にセッションを空にする特別なコードがあります。トランザクション
マネージャはこれをさらに一歩進め、リクエストの間に行なわれたすべての変更
をコミットするか、またはリクエストの間に例外が上げられた場合、変更をロール
バックします。ビューがいつ/どのようにコミットが生じるかをカスタマイズ
したい場合、 ZopeTransactionExtension はモジュールレベルの API を提供します。


.. The upshot is that your view method does not have to call
.. ``DBSession.commit()``: the transaction manager will do it for you. Also, it doesn't
.. have to put the changes in a try-except block because the transaction manager
.. will call ``DBSession.rollback()`` if an exception occurs. (Many Pylons actions don't
.. do this so they're technically incorrect.) A side effect is that you *cannot*
.. call ``DBSession.commit()`` or ``DBSession.rollback()`` directly. If you want
.. to precisely control when something is committed, you'll have to do it this way:

結論は、ビューメソッドが ``DBSession.commit()`` を呼ぶ必要がないという
ことです: トランザクションマネージャが代わりにそれをします。さらに、例外が
生じればトランザクションマネージャが ``DBSession.rollback()`` を呼ぶので、
変更を try-except ブロックに置く必要はありません (多くの Pylons アクション
はこれをしないので、それらは厳密には正しくありません)。 副作用は、
``DBSession.commit()`` あるいは ``DBSession.rollback()`` を直接呼ぶこと
ができないということです。何かがいつコミットされるか正確にコントロール
したければ、このようにそれをしなければなりません:


::

    import transaction

    transaction.commit()
    # Or:
    transaction.rollback()


.. There's also a ``transaction.doom()`` function which you can call to prevent
.. *any* database writes during this request, including those performed by
.. other parts of the application. Of course, this doesn't affect changes that
.. have already been committed.

さらに ``transaction.doom()`` 関数もあります。これは、アプリケーションの
他の部分によって行なわれるものを含めてこのリクエストの間の *あらゆる*
データベース書き込みを防ぐために呼ぶことができるものです。
もちろん、これは、既にコミットされた変更には影響しません。


.. You can customize the circumstances under which an automatic rollback occurs by
.. defining a "commit veto" function. This is described in the pyramid_tm
.. documentation.

"commit veto" 関数を定義することにより、自動ロールバックが生じる状況を
カスタマイズすることができます。これは pyramid_tm ドキュメンテーションに
述べられています。


.. Using traversal as a model

トラバーサルをモデルとして使う
==============================

.. Pylons doesn't have a traversal mode, so you have to fetch database objects in
.. the view code. Pyramid's traversal mode essentially does this for you,
.. delivering the object to the view as its *context*, and handling "not found"
.. for you. Traversal resource tree thus almost looks like a second kind of model,
.. separate from ``models``. (It's typically defined in a ``resources`` module.)
.. This raises the question of, what's the difference between the two?  Does it
.. make sense to convert my model to traversal, or to traversal under the control
.. of a route?  The issue comes up further with authorization, because Pyramid's
.. default authorization mechanism is designed for permissions (an access-control
.. list or ACL) to be attached to the *context* object. These are advanced
.. questions so we won't cover them here. Traversal has a learning curve, and it
.. may or may not be appropriate for different kinds of applications.
.. Nevertheless, it's good to know it exists so that you can explore it gradually
.. over time and maybe find a use for it someday.

Pylons はトラバーサルモードを持っていません。したがって、ビューコード
中のデータベースオブジェクトを取って来なければなりません。Pyramid の
トラバーサルモードは、本質的にこれを自動的に行います。オブジェクトを
*context* としてビューに渡し、 "not found" を扱います。トラバーサル
リソースツリーはこのように、 ``models`` とは独立した、ほとんど 2 番目の
種類のモデルのように見えます (それは典型的に ``resources`` モジュールに
定義されています)。これは問題を提起します。 2 つの間の違いは何ですか?
私のモデルをトラバーサルや route の管理下でのトラバーサルに変換すること
に意味がありますか? これは認可に関して特に問題になります。なぜなら
Pyramid のデフォルトの認可機構が *context* オブジェクトに取り付けられた
許可 (アクセスコントロールリストあるいは ACL) に対して設計されている
からです。これらは高度な質問なので、ここではそれらを扱いません。
トラバーサルには学習曲線があります。また、異なる種類のアプリケーション
にとって適切かもしれないし、適切ではないかもしれません。とはいえ、
それが存在するということを知っておくのは良いことです。そうすれば、
時間をかけて徐々にそれを調査して、おそらくいつかその使い道を見つける
ことができるでしょう。


.. SQLAHelper and a "models" subpackage

SQLAHelper と "models" サブパッケージ
=====================================

.. Earlier versions of Akhet used the SQLAHelper library to organize engines and
.. sessions. This is no longer documented because it's not that much benefit. The
.. main thing to remember is that if you split *models.py* into a package, beware
.. of circular imports. If you define the ``Base`` and ``DBSession`` in
.. *models/\_\_ini\_\_.py* and import them into submodules, and the init module
.. imports the submodules, there will be a circular import of two modules
.. importing each other. One module will appear semi-empty while the other module
.. is running its global code, which could lead to exceptions. 

Akhet の初期バージョンでは、エンジンとセッションを構成するために
SQLAHelper ライブラリを使用していました。これはあまり有益ではないため、
もはや文書化されません。覚えておくべき重要なことは、 *models.py* を
パッケージに分割した場合、循環インポートに用心する必要がある、
ということです。 *models/\_\_ini\_\_.py* に ``Base`` と ``DBSession`` を
定義して、サブモジュールでそれらをインポートして、さらに init モジュール
がサブモジュールをインポートすれば、互いにインポートする2つのモジュール
の循環インポートが発生します。あるモジュールのグローバルコードを実行
している間、別のモジュールは semi-empty に見えるでしょう。
これは例外を引き起こす可能性があります。


.. Pylons dealt with this by putting the Base and Session in a submodule,
.. *models/meta.py*, which did not import any other model modules. SQLAHelper
.. deals with it by providing a third-party library to store engines, sessions,
.. and bases. The Pyramid developers decided to default to the simplest case of
.. the putting entire model in one module, and let you figure out how to split it
.. if you want to.

Pylons は、サブモジュール (*models/meta.py*) に Base と Session を置き、
このモジュールでは他のモデルのモジュールをインポートしないことでこの問題
に対処しました。 SQLAHelper は、エンジン、セッションおよびベースクラスを
格納するためのサードパーティライブラリを提供することにより、この問題に
対処します。 Pyramid 開発者は、モデル全体を 1 つのモジュール中に置く最も
単純な場合をデフォルトすることに決定しました。そして、あなたがそうした
ければモデルを分割する方法が分かるようにしました。


.. Model Examples

モデルの例
==============

.. These examples were written a while ago so they don't use the transaction
.. manager, and they have yet at third importing syntax. They should work with
.. SQLAlchemy 0.6, 0.7, and 0.8.

これらの例はしばらく前に書かれたため、トランザクションマネージャを使って
いません。また、第三のインポート形式がまだ残っています。これらは
SQLAlchemy 0.6, 0.7, 0.8 で動くはずです。


.. A simple one-table model

単純な1テーブルのモデル
------------------------

::

    import sqlalchemy as sa
    import sqlalchemy.orm as orm
    import sqlalchemy.ext.declarative as declarative
    from zope.sqlalchemy import ZopeTransactionExtension as ZTE

    DBSession = orm.scoped_session(om.sessionmaker(extension=ZTE()))
    Base = declarative.declarative_base()

    class User(Base):
        __tablename__ = "users"

        id = sa.Column(sa.Integer, primary_key=True)
        name = sa.Column(sa.Unicode(100), nullable=False)
        email = sa.Column(sa.Unicode(100), nullable=False)


.. This model has one ORM class, ``User`` corresponding to a database table
.. ``users``. The table has three columns: ``id``, ``name``, and ``user``.

このモデルには1つの ORM クラス (データベーステーブル ``users`` に対応
する ``User``) があります。テーブルには3つのカラムがあります: ``id``,
``name``, ``user`` です。


.. A three-table model

3テーブルのモデル
-------------------

.. We can expand the above into a three-table model suitable for a medium-sized
.. application.

上記のモデルを、中規模のアプリケーションに適した 3 テーブルモデルへと
拡張することができます。


::

    import sqlalchemy as sa
    import sqlalchemy.orm as orm
    import sqlalchemy.ext.declarative as declarative
    from zope.sqlalchemy import ZopeTransactionExtension as ZTE

    DBSession = orm.scoped_session(om.sessionmaker(extension=ZTE()))
    Base = declarative.declarative_base()

    class User(Base):
        __tablename__ = "users"

        id = sa.Column(sa.Integer, primary_key=True)
        name = sa.Column(sa.Unicode(100), nullable=False)
        email = sa.Column(sa.Unicode(100), nullable=False)

        addresses = orm.relationship("Address", order_by="Address.id")
        activities = orm.relationship("Activity",
            secondary="assoc_users_activities")

        @classmethod
        def by_name(class_):
            """Return a query of users sorted by name."""
            User = class_
            q = Session.query(User)
            q = q.order_by(User.name)
            return q
        

    class Address(Base):
        __tablename__ = "addresses"

        id = sa.Column(sa.Integer, primary_key=True)
        user_id = foreign_key_column(None, sa.Integer, "users.id")
        street = sa.Column(sa.Unicode(40), nullable=False)
        city = sa.Column(sa.Unicode(40), nullable=False)
        state = sa.Column(sa.Unicode(2), nullable=False)
        zip = sa.Column(sa.Unicode(10), nullable=False)
        country = sa.Column(sa.Unicode(40), nullable=False)
        foreign_extra = sa.Column(sa.Unicode(100, nullable=False))

        def __str__(self):
            """Return the address as a string formatted for a mailing label."""
            state_zip = u"{0} {1}".format(self.state, self.zip).strip()
            cityline = filterjoin(u", ", self.city, state_zip)
            lines = [self.street, cityline, self.foreign_extra, self.country]
            return filterjoin(u"|n", *lines) + u"\n"


    class Activity(Base):
        __tablename__ = "activities"

        id = sa.Column(sa.Integer, primary_key=True)
        activity = sa.Column(sa.Unicode(100), nullable=False)


    assoc_users_activities = sa.Table("assoc_users_activities", Base.metadata,
        foreign_key_column("user_id", sa.Integer, "users.id"),
        foreign_key_column("activities_id", sa.Unicode(100), "activities.id"))
            
    # Utility functions
    def filterjoin(sep, *items):
        """Join the items into a string, dropping any that are empty.
        """
        items = filter(None, items)
        return sep.join(items)

    def foreign_key_column(name, type_, target, nullable=False):
        """Construct a foreign key column for a table.

        ``name`` is the column name. Pass ``None`` to omit this arg in the 
        ``Column`` call; i.e., in Declarative classes.

        ``type_`` is the column type.

        ``target`` is the other column this column references.

        ``nullable``: pass True to allow null values. The default is False
        (the opposite of SQLAlchemy's default, but useful for foreign keys).
        """
        fk = sa.ForeignKey(target)
        if name:
            return sa.Column(name, type_, fk, nullable=nullable)
        else:
            return sa.Column(type_, fk, nullable=nullable)


.. This model has a ``User`` class corresponding to a ``users`` table, an
.. ``Address`` class with an ``addresses`` table, and an ``Activity`` class with
.. ``activities`` table.  ``users`` is in a 1:Many relationship with
.. ``addresses``.  ``users`` is also in a Many:Many`` relationship with
.. ``activities`` using the association table ``assoc_users_activities``.  This is
.. the SQLAlchemy "declarative" syntax, which defines the tables in terms of ORM
.. classes subclassed from a declarative ``Base`` class. Association tables do not
.. have an ORM class in SQLAlchemy, so we define it using the ``Table``
.. constructor as if we weren't using declarative, but it's still tied to the
.. Base's "metadata".

このモデルには ``users`` テーブルに対応する ``User`` クラス、
``addresses`` テーブルに対応する ``Address`` クラス、 ``activities``
テーブルに対応する ``Activity`` クラスがあります。 ``users`` は
``addresses`` と 1:Many 関連を持っています。 ``users`` はさらに、関連
テーブル ``assoc_users_activities`` を使用して ``activities`` と
``Many:Many`` 関係を持っています。これは SQLAlchemy の "declarative"
構文です。それは宣言的な ``Base`` クラスからサブクラス化された ORM クラス
としてテーブルを定義します。関連テーブルは SQLAlchemy に ORM クラスを
持っていません。したがって、あたかも declarative を使用していないかのように
``Table`` コンストラクタを使用してそれを定義します。しかし、それはやはり
Base の「メタデータ」に紐付けられます。


.. We can add instance methods to the ORM classes and they will be valid for one
.. database record, as with the ``Address.__str__`` method. We can also define
.. class methods that operate on several records or return a query object, as with
.. the ``User.by_name`` method. 

``Address.__str__`` メソッドのように、 ORM クラスにインスタンスメソッド
を加えることができます。そうするとそれらは 1 つのデータベース・レコードで
有効になります。さらに、 ``User.by_name`` メソッドのように、いくつかの
レコード上で動作するか、クエリオブジェクトを返すクラスメソッドを定義する
ことができます。


.. There's a bit of disagreement on whether ``User.by_name`` works better as a
.. class method or static method. Normally with class methods, the first argument
.. is called ``class_`` or ``cls`` or ``klass`` and you use it that way throughout
.. the method, but in ORM queries it's more normal to refer to the ORM class by
.. its proper name. But if you do that you're not using the ``class_`` variable
.. so why not make it a static method? But the method does belong to the class in
.. a way that an ordinary static method does not. I go back and forth on this, and
.. sometimes assign ``User = class_`` at the beginning of the method. But none of
.. these ways feels completely satisfactory, so I'm not sure which is best.

``User.by_name`` がクラスメソッドとスタティックメソッドのどちらの方が
よりうまく機能するかということに関して、多少の意見の不一致があります。
通常クラスメソッドでは、最初の引数は ``class_`` または ``cls``
あるいは ``klass`` と呼ばれます。そしてメソッド全体でそれが使われます。
しかし ORM クエリの中では、そのメソッドは固有の名前で ORM クラスを参照
する方が普通です。しかし、それをすると ``class_`` 変数を使用しません。
そうであれば、スタティックメソッドではいけないのでしょうか?  しかし、
通常のスタティックメソッドとは異なり、そのメソッドはクラスに属します。
私はこの考えを行ったり来たりして、メソッドの初めで ``User = class_`` を
割り当てることがあります。しかし、これらの方法のどれも完全に満足とは
感じません。したがって、私はどれが最良か分かりません。


.. Common base class

共通ベースクラス
-----------------

.. You can define a superclass for all your ORM classes, with common class methods
.. that all of them can use. It will be the parent of the declarative base:

すべての ORM クラスに対するスーパークラスを定義して、すべてのサブクラス
で使用できる共通のクラスメソッドを持たせることができます。それは
declarative base の親になるでしょう:


::

    class ORMClass(object):
        @classmethod
        def query(class_):
            return DBSession.query(class_)

        @classmethod
        def get(class_, id):
            return Session.query(class_).get(id)

    Base = declarative.declarative_base(cls=ORMClass)
    
    class User(Base):
        __tablename__ = "users"

        # Column definitions omitted


.. Then you can do things like this in your views:

その後、ビューの中でこのようなことができます:


::

    user_1 = models.User.get(1)
    q = models.User.query()


.. Whether this is a good thing or not depends on your perspective.

これが良いかどうかは、あなたの考え方次第です。


.. Multiple databases

複数データベース
------------------

.. The default configuration in the main function configures one database. To
.. connect to multiple databases, list them all in
.. *development.ini* under distinct prefixes. You can put additional engine
.. arguments under the same prefixes. For instance:

main 関数の中のデフォルト設定は1つのデータベースを設定します。複数の
データベースに接続するためには、それらをすべて別々のプリフィックスで
*development.ini* にリスト化してください。同じプリフィックスの下に
追加のエンジン引数を置くことができます。例えば:


.. code-block: ini

    sqlalchemy.url = postgresql://me:PASSWORD@localhost/mydb
    sqlalchemy.logging_name = maindb
    stats.url = sqlite:///%(here)s/scratch.sqlite
    stats.logging_name = sessionsdb


.. Then modify the main function to add each engine. You can also pass even more
.. engine arguments that override any same-name ones in the INI file.

次に、各エンジンを追加するように main 関数を修正してください。さらに、
INI ファイル中の同名の設定を上書きする追加のエンジン引数を渡すことが
できます。


::

    engine = sa.engine_from_config(settings, prefix="sqlalchemy.",
        pool_recycle=3600, convert_unicode=True)
    stats = sa.engine_from_config(settings, prefix="stats.")


.. At this point you have a choice. Do you want to bind different tables to
.. different databases in the same DBSession? That's easy:

この時点で選択肢があります。同じ DBSession の内で異なるテーブルを異なる
データベースに結び付けたいですか? それは容易です:


::

    DBSession.configure(bind={models.Person: engine, models.Score: stats})


.. The keys in the ``binds`` dict can be SQLAlchemy ORM classes, table objects, or
.. mapper objects.

``binds`` 辞書中のキーは SQLAlchemy ORM クラス、テーブルオブジェクト
あるいはマッパーオブジェクトになります。


.. But some applications prefer multiple DBSessions, each connected to a different
.. database. Some applications prefer multiple declarative bases, so that
.. different groups of ORM classes have a different declarative base. Or perhaps
.. you want to bind the engine directly to the Base's metadata for low-level SQL
.. queries. Or you may be using a third-party package that defines
.. its own DBSession or Base. In these cases, you'll have to modify the model
.. itself, e.g., to add a DBSession2 or Base2. If the configuration is complex you
.. may want to define a model initialization function like Pylons does, so that
.. the top-level routine (the main function or a standalone utility) only has to
.. make one simple call. Here's a pretty elaborate init routine for a complex
.. application:

しかし、アプリケーションによっては各々が異なるデータベースに接続された
複数の DBSession を好みます。また、アプリケーションによっては複数の
declarative base を好みます。その結果、異なるグループの ORM クラスは異
なる declarative base を持つことになります。あるいは、低レベルの SQL
クエリのためにエンジンを Base のメタデータに直接 bind したい場合もある
でしょう。あるいは、それ自身の DBSession あるいは Base を定義するサード
パーティのパッケージを使用しているかもしれません。これらの場合では、
例えば DBSession2 や Base2 を加えるためにモデル自体を修正しなければ
ならないでしょう。構成が複雑な場合、 Pylons が行うようにモデル初期化関数を
定義すると良いかもしれません。その結果、トップレベルのルーチン (main
関数あるいはスタンドアロンのユーティリティ) は、単純な呼び出しを一回だけ
行えば済むことになります。これは、複雑なアプリケーション用のかなり精巧な
init ルーチンです:


::

    DBSession1 = orm.scoped_session(orm.sessionmaker(extension=ZTE())
    DBSession2 = orm.scoped_session(orm.sessionmaker(extension=ZTE())
    Base1 = declarative.declarative_base()
    Base2 = declarative.declarative_base()
    engine1 = None
    engine2 = None

    def init_model(e1, e2):
        # e1 and e2 are SQLAlchemy engines. (We can't call them engine1 and
        # engine2 because we want to access globals with the same name.)
        global engine1, engine2
        engine1 = e1
        engine2 = e2
        DBSession1.configure(bind=e1)
        DBSession2.configure(bind=e2)
        Base1.metadata.bind = e1
        Base2.metadata.bind = e2


.. Reflected tables

リフレクションテーブル
----------------------

.. Reflected tables pose a dilemma because they depend on a live database
.. connection in order to be initialized. But the engine is not known 
.. when the model is imported. This situation pretty much requires an
.. initialization function; or at least we haven't found any way around it.
.. The ORM classes can still be defined as module globals (not using the
.. declarative syntax), but the table definitions and mapper calls will have to be
.. done inside the function when the engine is known. Here's how you'd do it
.. non-declaratively:

リフレクションテーブルは初期化のために実際のデータベース接続に依存する
ので、ジレンマが生じます。しかし、モデルがインポートされる時点でエンジンは
未知です。この状況はほぼ確実に初期化関数を必要とします; あるいは、
少なくともそれを回避する方法は見つかっていません。 ORM クラスは今まで
通りモジュールのグローバル変数として (宣言的な構文を使用せずに)
定義することができます。しかし、テーブル定義およびマッパー呼び出しは、
エンジンが既知になった時点で関数の内部で行われなければならないでしょう。
これは、それをどのように非宣言的に行うかを示したものです:


::

    DBSession = orm.scoped_session(orm.sessionmaker(extension=ZTE())
    # Not using Base; not using declarative syntax
    md = sa.MetaData()
    persons = None   # Table, set in init_model().

    class Person(object):
        pass

    def init_model(engine):
        global persons
        DBSession.configure(bind=engine)
        md.bind = engine
        persons = sa.Table("persons", md, autoload=True, autoload_with=engine)
        orm.mapper(Person, persons)


.. With the declarative syntax, we *think* Michael Bayer has posted recipies for
.. this somewhere, but you'll have to poke around the SQLAlchmey planet to find
.. them. At worst you could put the entire declarative class inside the init_model
.. function and assign it to a global variable.

宣言的な構文に関して、Michael Bayer はこのためのレシピをどこかに投稿し
ていたと *思います* が、それらを見つけるためには SQLAlchmey 界隈を
あちこち覗き回る必要があるでしょう。最悪の場合、declarative クラス
全体を init_model 関数の内部に置き、グローバル変数にそれを代入することが
できます。


.. _Engine Configuration: http://www.sqlalchemy.org/docs/core/engines.html
.. _Dialects: http://www.sqlalchemy.org/docs/dialects/index.html
.. _Configuring Logging: http://www.sqlalchemy.org/docs/core/engines.html#configuring-logging
.. _scoped session: http://www.sqlalchemy.org/docs/orm/session.html#contextual-thread-local-sessions
.. _Using a Commit Veto: http://docs.repoze.org/tm2/#using-a-commit-veto

.. include:: ../links.rst
