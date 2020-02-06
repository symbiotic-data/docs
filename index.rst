.. docs documentation master file, created by
   sphinx-quickstart on Sun Feb  2 17:04:57 2020.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Home
====

Symbiotic-Data is an initiative intending to standardize
primitive data types and their serialization formats, on different platforms, programming languages, and target
encodings.

Quick Links
-----------

+---------------+------------------------------------------------------------------+
| GitHub        | `github.com/symbiotic-data <https://github.com/symbiotic-data>`_ |
+---------------+------------------------------------------------------------------+
| Home Page     | `symbiotic-data.io <https://symbiotic-data.io>`_                 |
+---------------+------------------------------------------------------------------+
| Documentation | `docs.symbiotic-data.io <https://docs.symbiotic-data.io>`_       |
+---------------+------------------------------------------------------------------+

Motivation
----------

Different platforms have different paradigms, capabilities, preconceptions, and opinions on how data should
be formatted and stored internally, and this affects how the language or platform naturally tries to encode
that data. This project aims to clarify some of those differences, and formalize data serialization standards.
It is trying to act as a `Rosetta Stone <https://en.wikipedia.org/wiki/Rosetta_Stone>`_ for various languages.

Most implementations of Symbiotic Data should be as painless and mostly cost-free ---
ideally being a *no-op*. But for some instances, it is necessary to restrict and refine a
platform's natural understanding of a data type, in order to meet the criteria that constitutes its
definition in Symbiotic-Data.

Similarly, serialization and de-serialization for Symbiotic-Data should be as simple, intuitive, and fast
as possible, and rigorously defined for every data type.

----------------

Supported Types
===============

All supported types can be found in the documentation under :ref:`Data-Types <data>`. The
following classes of data are defined:

- :ref:`Primitives <primitives>`: Elementary data types supported by most programming languages --- Integers,
  Floating-point numbers, Boolean values, and Strings.

- :ref:`Casual Data <casual>`: Every-day useful data types for various tasks --- Chronological data (like time,
  dates, etc.), and Network-relevant data (URIs, email addresses, etc.) are included under this classification.

- :ref:`Primitive Composites <primitivecomposites>`: Elementary *container* data types --- Fixed-size Arrays,
  Varying-size Vectors, Tuples, and Tagged Unions.

  We understand not every language supports parametric polymorphism and cannot implement "container types".
  However, the behavior is defined such that the user's specialization is still qualified under the same
  umbrella.

- :ref:`Sophisticated Composites <sophisticatedcomposites>`: Specialty container data types --- Mappings,
  Recursive Mappings (`Tries <https://en.wikipedia.org/wiki/Trie>`_), and other complex containers may be placed
  in here.

  Sets (unique vectors) are not included due to this being a *serialization* definition library, not a
  data type library in generally. Similarly, qualified mappings are also not included (i.e. a ``HashMap``),
  because we are only concerned with the different methods of serializing data, not storing it.

Supported Target Encodings
==========================

Currently, only two target encodings are defined for Symbiotic-Data:

- `JSON <https://en.wikipedia.org/wiki/JSON>`_ (JavaScript Object Notation), because of its wide utility and
  usage throughout the Internet --- it is also a very likely suspect for incorrect encodings between languages.
- Raw binary stream --- binary serialization is the foundation for all data storage and communication, and
  is therefore the foundation for this project as well. All types have strict definitions for how they're stored
  in a byte-wise (8-bit) string.


-----------------

Test Suite
==========

The backbone of the *insurance policy* that guarantees these types are indeed *correctly* serialized
and interpreted is due to the :ref:`Symbiote Test Suite <testsuite>`: a protocol for
randomly generating data, and communicating that data over-the-wire to another platform.

Every implementation of Symbiotic-Data should also implement this test suite and protocol, to verify
its correct behavior in accordance with the standard.

Properties
----------

The types defined in Symbiotic-Data may satisfy the definitions of certain algebraic objects, and by
extension should have certain properties of behavior associated with their operations. You can see
what properties and objects we use in the test suites in the :ref:`Algebraic Properties <properties>`
page.

.. todo::
   Define the ``Topic`` associated with each Symbiotic-Data type.

.. todo::
   Define the security and client identifier enrichment mechanisms for each serialization target;

   ZeroMQ is used for binary data, over CurveMQ, with the server public key available in a TXT DNS record.

   WebSocket is used for JSON, and secured through a TLS connections. However, the testing protocol is
   enriched with a UUID tag for identifying the client connected, similar to Router-Dealer sockets in
   ZeroMQ.

.. todo::
   List the reference implementation server links for the serialization targets: ``wss://ws.symbiotic-data.io``
   and ``tcp://zmq.symbiotic-data.io``

Supported Platforms
-------------------

Currently the supported platforms are:

- Haskell

  - `symbiotic data implementation <https://github.com/symbiotic-data/symbiotic-haskell>`_
  - `symbiote test suite <https://hackage.haskell.org/package/symbiote>`_
- PureScript

  - `symbiote test suite <https://pursuit.purescript.org/package/purescript-symbiote>`_

There are planned implementations for:

- JavaScript (by use of the PureScript implementation)
- Rust
- Java / Clojure / Kotlin
- Objective-C / Swift
- C# / F#
- C / C++
- PHP
- Ruby
- Python
- Go
- D

If you have any other requested implementations or contributions, feel free to open an issue or pull request!



.. toctree::
   :maxdepth: 2
   :caption: Contents:

   Data Types <data>
   Algebraic Properties <properties>
   Test Suite <testsuite>
   Test Suite Types <testsuitetypes>
