.. Pyramid for Pylons Users

Pylons ユーザのための Pyramid
+++++++++++++++++++++++++++++

:Updated: 2012-06-12
:Versions: Pyramid 1.3
:Author: Mike Orr
:Contributors:

.. This guide discusses how Pyramid 1.3 differs from Pylons 1, and a few ways to
.. make it more like Pylons. The guide may also be helpful to readers coming from
.. Django or another Rails-like framework. The author has been a Pylons developer
.. since 2007. The examples are based on Pyramid's default SQLAlchemy application
.. and on the Akhet_ demo. 

このガイドでは、 Pyramid 1.3 が Pylons 1 とどのように異なるのかについて、
そして Pyramid を Pylons のようにするいくつかの方法について議論します。
このガイドは、さらに Django やその他の Rails 風のフレームワーク出身の読者
にも役立つかもしれません。著者は 2007 年以来 Pylons 開発者でした。
(このガイドの中の) 例は Pyramid のデフォルト SQLAlchemy アプリケーション
および Akhet_ デモに基づいています。


.. If you haven't used Pyramid yet you can read this guide to get an overview of
.. the differences and the Pyramid API. However, to actually start using Pyramid
.. you'll want to read at least the first five chapters of the `Pyramid
.. manual`_ (through `Creating a Pyramid Project`_) and go through the Tutorials_.
.. Then you can come back to this guide to start designing your application, and
.. skim through the rest of the manual to see which sections cover which topics.

Pyramid をまだ使用したことがなければ、その違いや Pyramid API の概観を
得るためにこのガイドを読むことができます。しかしながら、実際に Pyramid を
使用し始めるためには、少なくとも `Pyramid manual`_ の最初の 5章
(`Creating a Pyramid Project`_ まで) を読んで、 Tutorials_ を見た方が
良いでしょう。その後、アプリケーションの設計を始めるためにこのガイドに
戻り、どの節がどんなトピックをカバーをするかを確かめるためにマニュアルの
残りをざっと読み始めることができます。


.. toctree::
   :maxdepth: 1

   intro
   launch
   ini_file
   main
   models
   views
   examples
   request
   templates
   exceptions
   static
   sessions
   deployment
   auth
   other
   migrate


.. include::  ../links.rst
