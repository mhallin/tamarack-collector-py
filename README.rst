===============================
 Tamarack Collector for Python
===============================

Python implementation of the Tamarack_ collector library, specifically
for Django_. It could potentially be extended to general WSGI support,
but the improved diagnostics gained by a deeper integration with a
specific framework weighs heavier currently.

Installation
============

Via Pip:

.. code:: sh

   pip install git+ssh://github.com/mhallin/tamarack-collector-py

Then, in your ``settings.py``, add the middleware to your
``MIDDLEWARE_CLASSES`` *at the top*:

.. code:: python

   MIDDLEWARE_CLASSES = (
       'tamarackcollector.django.TamarackMiddleware',

       # Your other middleware below
       # ...
   )

Note again that it should be at the top! Django middlewares are
processed in order, so if Tamarack is below another, it wont be able
to measure time taken by that middleware.

Finally, add this at the top of your ``manage.py`` (and ``wsgi.py`` if
you are using it):

.. code:: python

   import tamarackcollector
   tamarackcollector.wrap()

Settings
========

You will need to set two keys in ``settings.py`` in order to use the
collector:

* ``TAMARACK_APP_ID``, a string identifying your application in the
  Explorer. It should be different in different environments, but the
  same across servers in the same environment. For example, ``'My
  App - Production'``.

* ``TAMARACK_URL``, the URL of the Tamarack server. For example,
  ``http://127.0.0.1:3000``.

Performance Impact
==================

It is unavoidable for this library to have a non-zero impact on the
performance of your application. However, it is implemented with the
intent to cause the least amount of per-request overhead as
possible. After each request, data is handed off to a worker process
which summarizes it and sends it over to the Tamarack Receiver. The
library is using the ``multiprocessing`` module of Python.

In order to provide a more detailed breakdown of your application's
performance, the collector wraps a bunch of libraries included in your
application. This ranges from template rendering to database cursor
functions. If you don't want this, remove the
``tamarackcollector.wrap()`` calls.

Supported Libraries
===================

Application Servers:

* Django_

Database Libraries:

* The standard ``sqlite3`` module

.. _Tamarack: https://github.com/mhallin/tamarack
.. _Django: https://www.djangoproject.com/
