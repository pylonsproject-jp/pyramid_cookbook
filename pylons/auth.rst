.. Authentication and Authorization

認証と認可
++++++++++++++++++++++++++++++++

.. *This chapter is contributed by Eric Rasmussen.*

*本章は Eric Rasmussen によって寄稿されました。*


.. Pyramid has built-in authentication and authorization capibalities that make it
.. easy to restrict handler actions. Here is an overview of the steps you'll
.. generally need to take:

Pyramid にはハンドラアクションを簡単に制限するための内蔵の認証および
認可の機能があります。これは、一般的に行う必要のあるステップの概観です:


.. 1) Create a root factory in your model that associates allow/deny directives
..    with groups and permissions
.. 2) Create users and groups in your model
.. 3) Create a callback function to retrieve a list of groups a user is subscribed to based on their user ID
.. 4) Make a "forbidden view" that will be invoked when a Forbidden exception is
..    raised.
.. 5) Create a login action that will check the username/password and remember the
..    user if successful
.. 6) Restrict access to handler actions by passing in a
..    permission='somepermission' argument to ``@view_config``.
.. 7) Wire it all together in your config

1) モデルの中に allow/deny ディレクティブとグループおよびパーミッション
   を関連付ける root ファクトリを作る。
2) モデルの中にユーザとグループを作る。
3) ユーザー ID に基づいてユーザが所属するグループのリストを検索する
   ためのコールバック関数を作成する。
4) Forbidden 例外が上げられた時に起動される "forbidden view" を作成する。
5) ユーザー名/パスワードをチェックして、成功すればユーザを remember する
   ログインアクションを作成する。
6) ``@view_config`` に permission='somepermission' 引数を渡すことで
   ハンドラアクションへのアクセスを制限する。
7) 設定中でそれらすべてを結びつける。


.. You can get started by adding an import statement and custom root factory to
.. your model:

モデルにインポート文とカスタム root ファクトリを加えることで始めます:

::

    from pyramid.security import Allow, Everyone

    class RootFactory(object):
        __acl__ = [ (Allow, Everyone, "everybody"),
                    (Allow, "basic", "entry"),
                    (Allow, "secured", ("entry", "topsecret"))
                  ]
        def __init__(self, request):
            pass


.. The custom root factory generates objects that will be used as the context of
.. requests sent to your web application. The first attribute of the root factory
.. is the ACL, or access control list. It's a list of tuples that contain a
.. directive to handle the request (such as Allow or Deny), the group that is
.. granted or denied access to the resource, and a permission (or optionally a
.. tuple of permissions) to be associated with that group. 

カスタム root ファクトリは、ウェブアプリケーションに送られたリクエスト
のコンテキストとして使用されるオブジェクトを生成します。 root ファクトリ
の最初の属性は ACL つまりアクセスコントロールリストです。それは、
(Allow または Deny のような) リクエストを扱うディレクティブ、リソースへの
アクセスが許可されるか拒否されるグループ、そしてそのグループに関連する
パーミッション (オプションでパーミッションのタプル) を含むタプルのリスト
です。


.. The example access control list above indicates that we will allow everyone to
.. view pages with the 'everybody' permission, members of the basic group to view
.. pages restricted with the 'entry' permission, and members of the secured group
.. to view pages restricted with either the 'entry' or 'topsecret' permissions.
.. The special principal 'Everyone' is a built-in feature that allows any person
.. visiting your site (known as a principal) access to a given resource.

上記の例のアクセスコントロールリストは、全員が 'everybody' パーミッションで
ページを見ること、 basic グループのメンバーが 'entry' パーミッション
で制限されたページを見ること、そして secured グループのメンバーが
'entry' あるいは 'topsecret' パーミッションのいずれかで制限されたページ
を見ることを許可すると示しています。特別な principal 'Evenryone' は内蔵
の機能で、サイトを訪れた人 (これは principal として知られる) なら誰でも、
与えられたリソースへのアクセスを許可します。


.. For a user to login, you can create a handler that validates the login and
.. password (or any additional criteria) submitted through a form. You'll
.. typically want to add the following imports:

ユーザがログインするために、フォームによって送信されたログインおよび
パスワード (あるいは任意の追加の基準) を有効にするハンドラを作成することが
できます。典型的には次のインポートを加えると良いでしょう:


::

    from pyramid.httpexceptions import HTTPFound
    from pyramid.security import remember, forget

 
.. Once you validate a user's login and password against the model, you can set
.. the headers to "remember" the user's ID, and then you can redirect the user to
.. the home page or url they were trying to access:

モデルに対してユーザのログインおよびパスワードを一旦検証すれば、ヘッダー
にユーザの ID を「覚えさせる」 ("remember") ことができます。そして、ユーザを
ホームページまたは彼らがアクセスしようとした url にリダイレクトさせることが
できます:


::

    # retrieve the userid from the model on valid login
    headers = remember(self.request, userid)
    return HTTPFound(location=someurl, headers=headers)


.. Note that in the call to the remember function, we're passing in the user ID we
.. retrieved from the database and stored in the variable 'userid' (an arbitrary
.. name used here as an example). However, you could just as easily pass in a
.. username or other unique identifier. Whatever you decide to "remember" is what
.. will be passed to the groupfinder callback function that returns a list of
.. groups a user belongs to. If you import ``authenticated_userid``, which is a
.. useful way to retrieve user information in a handler action, it will return the
.. information you set the headers to "remember".

remember 関数の呼び出しでは、ユーザー ID を渡していることに注意して
ください。それはデータベースから検索されて、変数 'userid' (例としてここで
使われている任意の名前) に格納されています。しかし、より簡単に、ユーザー
名あるいは他のユニークな識別子を渡すことで同じことができます。 remember
するために決定すべきものは、ユーザが所属するグループのリストを返す
groupfinder コールバック関数に渡されるものです。 ``authenticated_userid``
(それはハンドラアクションでユーザ情報を検索する便利な方法です) をインポート
すれば、それは "remember" するためにヘッダーに設定するための情報を返します。


.. To log a user out, you "forget" them, and use HTTPFound to redirect to another
.. url:

ユーザをログアウトさせるためには、それらを「忘れ」て ("forget")、 HTTPFound
を使用して別の url にリダイレクトします:


::

    headers = forget(self.request)
    return HTTPFound(location=someurl, headers=headers)


.. Before you restrict a handler action with a permission, you will need a
.. callback function to return a list of groups that a user ID belongs to. Here is
.. one way to implement it in your model, in this case assuming you have a Groups
.. object with a groupname attribute and a Users object with a mygroups relation
.. to Groups:

ハンドラアクションをパーミッションによって制限する前に、ユーザー ID が
所属するグループのリストを返すコールバック関数を必要とするでしょう。これは
モデルの中でそれを実装する一つの方法です。この場合、 groupname 属性を持つ
Group オブジェクトと、 Group との mygroups 関係を持った User オブジェクト
があることを仮定しています:


::

    def groupfinder(userid, request):
        user = Users.by_id(userid)
        return [g.groupname for g in user.mygroups]


.. As an example, you could now import and use the @action decorator to restrict
.. by permission, and authenticated_userid to retrieve the user's ID from the
.. request:

例として、ここでパーミッションによって制限するために @action デコレータを、
リクエストからユーザ ID を検索するために authenticated_userid を、
インポートして使用することができます:


::

    from pyramid_handlers import action
    from pyramid.security import authenticated_userid
    from models import Users

    class MainHandler(object):
        def __init__(self, request):
            self.request = request
    
        @action(renderer="welcome.html", permission="entry")
        def index(self):
            userid = authenticated_userid(self.request)
            user = Users.by_id(userid)
            username = user.username
            return {"currentuser": username}


.. This gives us a very simple way to restrict handler actions and also obtain
.. information about the user. This example assumes we have a Users class with a
.. convenience class method called by_id to return the user object. You can then
.. access any of the object's attributes defined in your model (such as username,
.. email address, etc.), and pass those to a template as dictionary key/values in
.. your return statement.

これは、ハンドラアクションを制限して、さらにユーザに関する情報を得る
ための非常に簡単な方法です。この例では、 Users クラスがあり、それが
user オブジェクトを返すための by_id と呼ばれる便利クラスメソッドを
持っていると仮定しています。その後、モデルに定義されたオブジェクトの
任意の属性 (ユーザー名、電子メールアドレス、など) にアクセスし、
return 文でそれらを辞書の key/value としてテンプレートに渡すことができます。


.. If you would like a specific handler action to be called when a forbidden
.. exception is raised, you need to add a forbidden view.  This was covered
.. earlier, but for completelness:

forbidden 例外が上げられた時に特定のハンドラアクションを呼び出してほしければ、
forbidden ビューを加える必要があります。これは、以前にカバーされていますが、
完全性のために:


::

    @view_config(renderer='myapp:templates/forbidden.html',
                 context='pyramid.exceptions.Forbidden')
    @action(renderer='forbidden.html')
    def forbidden(request):
        ...


.. The last step is to configure __init__.py to use your auth policy. Make sure to
.. add these imports:

最後のステップは、 auth ポリシーを使用するために __init__.py を構成する
ことです。確実にこれらのインポートを加えてください:


::

    from pyramid.authentication import AuthTktAuthenticationPolicy
    from pyramid.authorization import ACLAuthorizationPolicy
    from .models import groupfinder


.. In your main function you'll want to define your auth policies so you can
.. include them in the call to Configurator:

main 関数では auth ポリシーを定義して、それらを Configurator への呼び出しに
含めると良いでしょう:


::

        authn_policy = AuthTktAuthenticationPolicy('secretstring', 
           callback=groupfinder)
        authz_policy = ACLAuthorizationPolicy()
        config = Configurator(settings=settings, 
           root_factory='myapp.models.RootFactory',
           authentication_policy=authn_policy,
           authorization_policy=authz_policy)
        config.scan()


.. The capabilities for authentication and authorization in Pyramid are very easy
.. to get started with compared to using Pylons and repoze.what. The advantage is
.. easier to maintain code and built-in methods to handle common tasks like
.. remembering or forgetting users, setting permissions, and easily modifying the
.. groupfinder callback to work with your model. For cases where it's manageable
.. to set permissions in advance in your root factory and restrict individual
.. handler actions, this is by far the simplest way to get up and running while
.. still offering robust user and group management capabilities through your
.. model. 

Pyramid における認証と認可に関する機能は、 Pylons や repoze.what を使う場合
と比較して始めるのが非常に簡単です。その利点は、共通のタスクを扱うコード
および内蔵の方法をメンテナンスするのがより簡単なことです。共通のタスクには、
ユーザを remember または forget する、パーミッションをセットする、
自分のモデルで動かすために groupfinder コールバックを簡単に修正すること、
などが含まれます。
root ファクトリで前もってパーミッションを設定して、個々のハンドラアクション
を制限することが管理可能な場合には、これは一方でモデルを通して頑健なユーザ
およびグループ管理能力を提供しながらも、運用することが断然単純な方法です。


.. However, if your application requires the ability to create/edit/delete
.. permissions (not just access through group membership), or you require the use
.. of advanced predicates, you can either build your own auth system (see the
.. Pyramid docs for details) or integrate an existing system like repoze.what.

しかしながら、アプリケーションが (グループ所属による単なるアクセスでは
なく) パーミッションを作成/編集/削除する能力を要求するか、高度な述語の
使用を要求する場合、自分の auth システムを構築するか (詳細に関しては
Pyramid ドキュメントを参照) 、あるいは repoze.what のような既存のシステ
ムを統合することができます。


.. You can also use "repoze.who" with Pyramid's authorization system if you want to
.. use Who's authenticators and configuration.

さらに、 "repoze.who" を Pyramid の認証システムと共に使用することも
(もし Who の認証と設定を使いたければ) できます。
