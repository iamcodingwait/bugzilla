.. _iis:

Microsoft IIS
#############

Bugzilla works with IIS as a normal CGI application. These instructions assume
that you are using Windows 7. Procedures for other versions are probably similar.

Begin by starting Internet Information Services (IIS) Manager.
:guilabel:`Start` --> :guilabel:`Administrators Tools` -->
:guilabel:`Internet Information Services (IIS) Manager`. Or run the command:

:command:`inetmgr`

Create a New Application
========================

Expand your :guilabel:`Server` until the :guilabel:`Default Web Site` shows
its children.

Right-click :guilabel:`Default Web Site` and select
:guilabel:`Add Application` from the menu.

Unde :guilabel:`Alias`, enter the alias for the website. This is the path
below the domain where you want Bugzilla to appear.

Under :guilabel:`Physical Path`, enter the path to Bugzilla,
:file:`C:\\Bugzilla`.

When finished, click :guilabel:`OK`.

Configure the Default Document
==============================

Click on the Application that you just created. Double-click on
:guilabel:`Default Document`, and click :guilabel:`Add` underneath the
Actions menu.

Under :guilabel:`Name`, enter ``index.cgi``.

All other default documents can be removed for this application.

.. warning:: Do not delete the default document from the :guilabel:`Default Website`.

Add Handler Mappings
====================

Ensure that you are at the Default Website. Under :guilabel:`IIS`,
double-click :guilabel:`Handler Mappings`. Under :guilabel:`Actions`, click
:guilabel:`Add Script Map`. You need to do this twice.

For the first one, set the following values (replacing paths if necessary):

* :guilabel:`Request Path`: ``*.pl``
* :guilabel:`Executable`: ``C:\Perl\bin\perl.exe "%s" %s``
* :guilabel:`Name`: ``Perl Script Map``

At the prompt select :guilabel:`Yes`.

.. note:: The ActiveState Perl installer may have already created an entry for
   .pl files that is limited to ``GET,HEAD,POST``. If so, this mapping should
   be removed, as Bugzilla's .pl files are not designed to be run via a web
   server.

For the second one, set the following values (replacing paths if necessary):

* :guilabel:`Request Path`: ``*.cgi``
* :guilabel:`Executable`: ``C:\Perl\bin\perl.exe "%s" %s``
* :guilabel:`Name`: ``CGI Script Map``

At the prompt select :guilabel:`Yes`.

Bugzilla Application
====================

Ensure that you are at the Bugzilla Application. Under :guilabel:`IIS`,
double-click :guilabel:`Handler Mappings`. Under :guilabel:`Actions`, click
:guilabel:`Add Script Map`.

Set the following values (replacing paths if necessary):

* :guilabel:`Request Path`: ``*.cgi``
* :guilabel:`Executable`: ``C:\Perl\bin\perl.exe -x"C:\Bugzilla" -wT "%s" %s``
* :guilabel:`Name`: ``Bugzilla``

At the prompt select :guilabel:`Yes`.

Now it's time to restart the IIS server to take these changes into account.
From the top-level menu, which contains the name of your machine, click
:guilabel:`Restart` under :guilabel:`Manage Server`. Or run the command:

:command:`iisreset`

Common Problems
===============

Bugzilla runs but it's not possible to log in
  You've probably configured IIS to use ActiveState's ISAPI DLL -- in other
  words you're using PerlEx, or the executable IIS is configured to use is
  :file:`PerlS.dll` or :file:`Perl30.dll`.

  Reconfigure IIS to use :file:`perl.exe`.

IIS returns HTTP 502 errors
  You probably forgot the ``-T`` argument to :file:`perl` when configuring the
  executable in IIS.

XMLRPC interface not working with IIS
  This is a known issue. See :bug:`708252`.
