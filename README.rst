=============================
lonetwin's pimped-up pythonrc
=============================

What is this ?
==============

This is a python script intended to improve on the default Python interactive
shell experience.

Unlike ipython_, bpython_ or any of the many other options out there, this is
not designed to be used as a separate interactive environment. The intent is to
keep it as a single file and use it as any other rcfile. This script relies
solely on the standard python library and will always remain
that way.

Demo
=====
|demo|

Usage
=====

The ``pythonrc`` file will be executed when the Python interactive shell is
started, if ``$PYTHONSTARTUP`` is in your environment and points to the file.

You could also simply make the file executable and call it directly.

Features
========

The file creates an InteractiveConsole_ instance and executes it. This instance
provides:

* colored prompts and pretty printing
* intelligent tab completion [1]_
    - without preceding text four spaces
    - with preceding text
        + names in the current namespace
        + for objects, their attributes/methods
        + for strings with a ``/``, pathname completion
        + module name completion in an import statement
* auto-indentation
* shortcut to open your ``$EDITOR`` with the last executed command (the ``\e``
  command)
* temporary escape to ``$SHELL`` or ability to execute a shell command and
  capturing the output in to the ``_`` variable (the ``!`` command)
* execution history
* convenient printing of doc stings (the '?' command) and search for entries in
  online docs (the '??' command)

If you have any other good ideas please feel free to submit pull requests or
issues.


A little history
================

Ever since around 2005_, I've been obsessed with tweaking my python interactive
console to have it behave the way I prefer. Despite multiple attempts I've failed to
embrace ipython on the command line because some of ipython's approach just
don't *fit my head*. Additionally, ipython is a full environment and I just need
some conveniences added to the default environment. This is why I started
maintaining my own pythonrc. I started eventually sharing it as a gist_ back in
2014 and now about 38 revisions later, I think it might just make sense to set
it up as a project so that I can accept pull requests, bug reports or
suggestions in case somebody bothers to use it and contribute back.


Known Issue
===========

The console is *not* `__main__`. The issue was first reported by @deeenes in the
gist_ I used to maintain. In essence, this code fails::

    >>> import timeit
    >>>
    >>> def getExecutionTime():
    ...     t = timeit.Timer("sayHello()", "from __main__ import sayHello")
    ...     return t.timeit(2)
    ...
    >>> def sayHello():
    ...     print("Hello")
    ...
    >>> print(getExecutionTime())
    Traceback (most recent call last):
      File "<console>", line 1, in <module>
      File "<console>", line 3, in getExecutionTime
      File "/usr/lib64/python2.7/timeit.py", line 202, in timeit
        timing = self.inner(it, self.timer)
      File "<timeit-src>", line 3, in inner
    ImportError: cannot import name sayHello
    >>>

There are two possible workarounds for this:

* When within the console, if you have to reference local names via
  `__main__`, remember to do it via `__main__.pymp.locals` instead, something
  like (for the example above)::

      ...
      def getExecutionTime():
          t = timeit.Timer("sayHello()", "from __main__ import pymp; sayHello = pymp.locals['sayHello']")
      ...

* Or in the pythonrc file, change the initialization of `ImprovedConsole` to
  accept `locals()`. That is something like this::

      pymp = ImprovedConsole(locals=locals())

  Although the downside of this is, doing it will pollute your console
  namespace with everything in the pythonrc file.


.. [1] Since python 3.4 the default interpreter also has tab completion enabled however it does not do pathname completion
.. _ipython: https://ipython.org/
.. _bpython: https://bpython-interpreter.org/
.. _InteractiveConsole: https://docs.python.org/3.6/library/code.html#code.InteractiveConsole
.. _2005: http://code.activestate.com/recipes/438813/
.. _gist: https://gist.github.com/lonetwin/5902720
.. |demo| image:: https://asciinema.org/a/100447.png
          :target: https://asciinema.org/a/100447?speed=2
