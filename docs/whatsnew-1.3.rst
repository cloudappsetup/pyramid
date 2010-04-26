What's New In :mod:`repoze.bfg` 1.3
===================================

This article explains the new features in :mod:`repoze.bfg` version
1.3 as compared to the previous 1.2 release.  It also documents
backwards incompatibilities between the two versions and deprecations
added to 1.3, as well as software dependency changes and notable
documentation additions.

Major Feature Additions
-----------------------

The major feature additions in 1.3 are:

- *Internationalization* (i18n) and *localization* (l10n) services

- *Exception views*

Internationalization and Localization
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:mod:`repoze.bfg` 1.3 offers internationalization (i18n) and
localization (l10n) subsystems that can be used to translate the text
of buttons, the text of error messages and other software- and
template-defined values into the native language of a user of your
application.

:mod:`repoze.bfg` i18n / l10n framework includes:

- Support for :term:`translation string` specifications.

- Tools allowing you to specify and work with :term:`gettext`
  :term:`message catalog` files to allow for text translation.

- :term:`Locale name` negotiation features.

- Translation and pluralization services.

For detailed documentation about :mod:`repoze.bfg`
internationalization and localization features, see
:ref:`i18n_chapter`.

Exception Views
~~~~~~~~~~~~~~~~

In :mod:`repoze.bfg` 1.3+, when you use an exception (anything that
inherits from the Python :exc:`Exception` builtin) as view context
argument, e.g.:

.. code-block:: python
   :linenos:

      from repoze.bfg.view import bfg_view
      from repoze.bfg.exceptions import NotFound
      from webob.exc import HTTPNotFound

      @bfg_view(context=NotFound)
      def notfound_view(request):
          return HTTPNotFound()

For the above example, when the :exc:`repoze.bfg.exceptions.NotFound`
exception is raised by any view or any root factory, the
``notfound_view`` view callable will be invoked and its response
returned.

Other normal view predicates can also be used in combination with an
exception view registration:

.. code-block:: python
   :linenos:

      from repoze.bfg.view import bfg_view
      from repoze.bfg.exceptions import NotFound
      from webob.exc import HTTPNotFound

      @bfg_view(context=NotFound, route_name='home')
      def notfound_view(request):
          return HTTPNotFound()

The above exception view names the ``route_name`` of ``home``, meaning
that it will only be called when the route matched has a name of
``home``.  You can therefore have more than one exception view for any
given exception in the system: the "most specific" one will be called
when the set of request circumstances match the view registration.
The only predicate that cannot be not be used successfully is
``name``.  The name used to look up an exception view is always the
empty string.

Existing (pre-1.3) normal views registered against objects inheriting
from :class:`Exception` will continue to work.  Exception views used
for user-defined exceptions and system exceptions used as contexts
will also work.

The feature can be used with any view registration mechanism
(``@bfg_view`` decorator, ZCML, or imperative
:meth:`repoze.bfg.configuration.Configurator.add_view` styles).

This feature was kindly contributed by Andrey Popp.

Minor Feature Additions
-----------------------

- Use :term:`Venusian` to perform ``@bfg_view`` decorator scanning
  rather than relying on a BFG-internal decorator scanner.  This means
  that user-defined decorators can be defined and found during
  :mod:`repoze.bfg` scanning (although documentation for doing so is
  currently not provided).

- It is now possible to turn on Chameleon template "debugging mode"
  for all Chameleon BFG templates by setting a BFG-related Paster
  ``.ini`` file setting named ``debug_templates``. The exceptions
  raised by Chameleon templates when a rendering fails are sometimes
  less than helpful.  ``debug_templates`` allows you to configure your
  application development environment so that exceptions generated by
  Chameleon during template compilation and execution will contain
  more helpful debugging information.  This mode is on by default in
  newly generated projects.  See also :ref:`debug_templates_section`.

- A new API method named
  :meth:`repoze.bfg.configuration.Configurator.derive_view` was
  added. This API can be used to generate a BFG view callable from a
  user-supplied function, instance, or class. This useful for external
  framework and plugin authors wishing to wrap callables supplied by
  their users which follow the same calling conventions and response
  conventions as objects that can be supplied directly to BFG as a
  view callable.

Backwards Incompatibilites
--------------------------

N/A

Deprecations and Behavior Differences
-------------------------------------

- The exception views feature replaces the need for the
  ``set_notfound_view`` and ``set_forbidden_view`` methods of the
  :class:`repoze.bfg.configuration.Configurator` as well as the
  :ref:`notfound_directive` and :ref:`forbidden_directive` ZCML
  directives.  Those methods and directives will continue to work for
  the foreseeable future, but they are deprecated in the
  documentation.

Dependency Changes
------------------

- A new install-time dependency on the ``venusian`` distribution was
  added.

- A new install-time dependency on the ``translationstring``
  distribution was added (internationalization).

- Chameleon 1.2.3 or better is now required (internationalization and
  per-template debug settings).

Documentation Enhancements
--------------------------

- Exception view documentation was added to the :ref:`hooks_chapter`
  narrative chapter.

- A new narrative chapter entitled :ref:`i18n_chapter` was added.

- The :ref:`environment_chapter` chapter was changed: documentation
  about the ``default_locale_name`` setting was added.

- A new API chapter for the :ref:`i18n_module` module was added.

- Documentation for the new :ref:`translationdir_directive` and
  :ref:`localenegotiator_directive` ZCML directives were added.

Licensing Changes
-----------------

- The Edgewall (BSD) license was added to the LICENSES.txt file, as
  some code in the :mod:`repoze.bfg.i18n` module derives from
  :term:`Babel` source.