.. docs documentation master file, created by
   sphinx-quickstart on Sun Feb  2 17:04:57 2020.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Home
====

Symbiotic-Data_ is an initiative intending to standardize
primitive data types and their serialization formats, on different platforms, programming languages, and target
encodings.

.. _Symbiotic-Data: https://github.com/symbiotic-data

Different platforms have different paradigms, capabilities, preconceptions, and opinions on how data should
be formatted and stored - this project aims to clarify some of those differences, and formalize the data,
so there's less corrections needing to be made by the programming community.

Most implementations of Symbiotic Data should be as painless and mostly cost-free -
ideally being a no-op. But for some instances, it is necessary to restrict and refine a
platform's natural understanding of a data type, in order to meet the common understanding among its peers.
Similarly, the act of serialization for Symbiotic Data should be as simple and fast as possible, and
rigorously defined for every data type.

----------------

The backbone of the insurance that these types are indeed *correctly* serialized and interpreted
across-the-board, is from the :ref:`Symbiote Test Suite <testsuite>`: a protocol for
randomly generating data, and testing the communication of that data over-the-wire to another platform.

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

   Test Suite <testsuite>
   Test Suite Types <testsuitetypes>
