..
  Technote content.

  See https://developer.lsst.io/restructuredtext/style.html
  for a guide to reStructuredText writing.

  Do not put the title, authors or other metadata in this document;
  those are automatically added.

  Use the following syntax for sections:

  Sections
  ========

  and

  Subsections
  -----------

  and

  Subsubsections
  ^^^^^^^^^^^^^^

  To add images, add the image file (png, svg or jpeg preferred) to the
  _static/ directory. The reST syntax for adding the image is

  .. figure:: /_static/filename.ext
     :name: fig-label

     Caption text.

   Run: ``make html`` and ``open _build/html/index.html`` to preview your work.
   See the README at https://github.com/lsst-sqre/lsst-technote-bootstrap or
   this repo's README for more info.

   Feel free to delete this instructional comment.

:tocdepth: 1

.. Please do not modify tocdepth; will be fixed when a new Sphinx theme is shipped.

.. sectnum::

.. TODO: Delete the note below before merging new content to the master branch.

.. note::

   **This technote is not yet published.**

   Organization of Database Schemas and Tables in the DM System

.. Add content here.

SQL-92 defined a database object hierarchy. The smallest units of that hierarchy include columns and
rows. Columns and rows are organized into tables and views. Multiples of tables and views are
organized into schemas. Multiples of schemas are organized into catalogs, and catalogs are organized
into clusters of catalogs.

The concept of clusters of catalogs predates SQL-92, but as defined in SQL-92 it roughly corresponds
to the group of catalogs, schemas, and tables that are *accessible* using a common connection (or
SQL-session). As our TAP service is serves as a common interface to a collection of catalogs, we map
that concept to our TAP interface; a TAP instance is a cluster of catalogs.

Catalog creation was never standardized. Some databases, such as MySQL, don't implement teh

Catalog creation is implementation dependent and therefore not part of the SQL standard

A catalog, as defined in SQL-92:

[SQL-92, Section - 4.13 - Clusters of catalogs] A cluster is an implementation-defined collection of
catalogs. Exactly one cluster is associated with an SQL-session and it defines the totality of the
SQL-data that is available to that SQL-session.

-  An instance of a cluster is described by an instance of a definition schema. Given some SQL-data
   object, such as a view, a constraint, a domain, or a base table, the definition of that object,
   and of all the objects that it directly or indirectly references, are in the same cluster of
   catalogs. For example, no and no can "cross" a cluster boundary.

-  Whether or not any catalog can occur simultaneously in more than one cluster is
   implementation-defined.

-  Within a cluster, no two catalogs have the same name.

Postgres
--------

Postgres is the closest in spirit to the SQL-92 specification. A Postgres server is normally
deployed as a single cluster listening for connections on a specific port [5432], though Postgres
also allows for the creation of additional clusters listening to different ports. Postgres does not
allow joins across cluster boundaries, nor does Postgres allow for joins across catalog boundaries.

Due to the restriction of joining across catalog boundaries in Postgres, it is not necessary to
fully qualify a table name of a query with the catalog, as catalogs are always specified manually in
the connection.

Oracle
------

In Oracle, a catalog is roughly equivalent to a service; though an Oracle process can manage
multiple services. Joins cannot be performed across service boundaries in Oracle. In Oracle, a
schema MUST be the name of a user.

MySQL
-----

A MySQL instance is a single catalog in SQL-92 terms. You can't join across MySQL instances.

Note: MySQL also calls schemas databases.

SQLite
------

Note: SQLite's semantics are not considered in this tech note, this section is included for
completeness.

A SQLite file is roughly equivalent to a schema. A SQLite *process* can dynamically attach multiple
files to the process under different names, though this feature is rarely used. In this sense, a
SQLite process is roughly equivalent to a single catalog.

Namespacing
===========

Using MySQL as the lowest common denominator, our system will allow users to refer to catalogs for
queries. This means that we must have identifiers that correspond to the following database objects
in the following systems:

-  For Oracle, we refer to the Oracle service as a catalog.

-  Connection information must include the host, port, and service name in the database URI.

-  For MySQL, we refer to the MySQL instance as a catalog.

-  Connection information must include host and port of a MySQL in the database URI.

-  For Postgres, we refer to the catalog of a specific cluster as a catalog.

-  Connection information must include the host and port in the database URI.

Joins
=====

Joins are NEVER guaranteed across catalog boundaries. Joins are ALWAYS guaranteed across schema
boundaries, provided a user is is authorized to ``SELECT`` on all schemas.

Catalogs Naming
===============

As we use our TAP interface to implement a cluster of catalogs, catalogs MUST be explicitly
registered in our TAP service for resolution when executing queries. Some amount of coordination
will be necessary to prevent name clashes.

Proposed catalog names
----------------------

``lsst`` will always refer to the production instance (service) of the consolidated LSST database.
 - Should a development Oracle service be deployed to the consolidate database, that ``lsst_dev``
   would refer to that service. 

``mysql_dev`` will refer to the current MySQL instance. 

``qserv`` is reserved for the production instance of qserv. 

``qserv_pdac`` will refer to the Qserv instance in the PDAC. 

``alerts`` is reserved for the production instance of the alerts database.



.. Do not include the document title (it's automatically added from metadata.yaml).

.. .. rubric:: References

.. Make in-text citations with: :cite:`bibkey`.

.. .. bibliography:: local.bib lsstbib/books.bib lsstbib/lsst.bib lsstbib/lsst-dm.bib lsstbib/refs.bib lsstbib/refs_ads.bib
..    :encoding: latex+latin
..    :style: lsst_aa
