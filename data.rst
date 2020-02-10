.. _data:

Symbiotic Data
==============

.. _primitives:

Primitives
----------

Unit
~~~~

.. code-block:: haskell

   data Unit = Unit

JSON
^^^^

Treats ``Unit`` as an empty string ``""``

.. code-block:: haskell

   encodeJson :: Unit -> Json
   encodeJson Unit = stringAsJson ""

Binary
^^^^^^

Stores it as the byte ``0``

.. code-block:: haskell

   encodeBinary :: Unit -> ByteString
   encodeBinary Unit = byteAsByteString 0

Symbiote Test Suite Instance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Topic
*****

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Unit``.

.. code-block:: none

   "Unit"

-------------

Boolean
~~~~~~~

.. code-block:: haskell

   data Boolean = True | False

JSON
^^^^

Uses standard JSON Boolean

.. code-block:: haskell

   encodeJson :: Boolean -> Json
   encodeJson x = case x of
     True -> true
     False -> false

Binary
^^^^^^

Uses standard bytes ``0`` as false, ``1`` as true

.. code-block:: haskell

   encodeBinary :: Boolean -> ByteString
   encodeBinary x = case x of
     True -> byteAsByteString 1
     False -> byteAsByteString 0

Symbiote Test Suite Instance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Topic
*****

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Boolean``.

.. code-block:: none

   "Boolean"

-------------

Integral
~~~~~~~~

Signed
^^^^^^

Int8
****

Range varies from ``-2^7`` to ``2^7-1``

.. code-block:: haskell

   data Int8 = -2^7 | -2^7+1 | ... | 2^7-2 | 2^7-1

JSON
""""

Uses standard JSON Integers

.. code-block:: haskell

   encodeJson :: Int8 -> Json
   encodeJson x = intAsJson x

Binary
""""""

``Int8`` s are converted to `Uint8`s before storing as a byte - where the negative range is stored as
the upper values in the ``Uint8``.

.. code-block:: haskell

   encodeBinary :: Int8 -> ByteString
   encodeBinary x =
     if x >= 0
     then byteAsByteString (intAsUint x)
     else byteAsByteString ((intAsUint (2^7 + x)) + 2^7)

Symbiote Test Suite Instance
""""""""""""""""""""""""""""

- Topic

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Int8``.

.. code-block:: none

   "Int8"

-------------

Int16
*****

Range varies from ``-2^15`` to ``2^15-1``

.. code-block:: haskell

   data Int16 = -2^15 | -2^15+1 | ... | 2^15-2 | 2^15-1

JSON
""""

Uses standard JSON Integers

.. code-block:: haskell

   encodeJson :: Int16 -> Json
   encodeJson x = intAsJson x

Binary
""""""

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

.. code-block:: haskell

   encodeBinary :: Int16 -> ByteString
   encodeBinary x = intAsByteStringBE x

.. code-block:: haskell

   encodeBinary :: Int16 -> ByteString
   encodeBinary x = intAsByteStringLE x

Symbiote Test Suite Instance
""""""""""""""""""""""""""""

- Topic

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Int16``.

.. code-block:: none

   "Int16"

-------------

Int32
*****

Range varies from ``-2^31`` to ``2^31-1``

.. code-block:: haskell

   data Int32 = -2^31 | -2^31+1 | ... | 2^31-2 | 2^31-1

JSON
""""

Uses standard JSON Integers

.. code-block:: haskell

   encodeJson :: Int32 -> Json
   encodeJson x = intAsJson x

Binary
""""""

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

.. code-block:: haskell

   encodeBinary :: Int32 -> ByteString
   encodeBinary x = intAsByteStringBE x

.. code-block:: haskell

   encodeBinary :: Int32 -> ByteString
   encodeBinary x = intAsByteStringLE x

Symbiote Test Suite Instance
""""""""""""""""""""""""""""

- Topic

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Int32``.

.. code-block:: none

   "Int32"

-------------

Int64
*****

Range varies from ``-2^63`` to ``2^63-1``

.. code-block:: haskell

   data Int64 = -2^63 | -2^63+1 | ... | 2^63-2 | 2^63-1

JSON
""""

Uses standard JSON Integers

.. code-block:: haskell

   encodeJson :: Int64 -> Json
   encodeJson x = intAsJson x

Binary
""""""

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

.. code-block:: haskell

   encodeBinary :: Int64 -> ByteString
   encodeBinary x = intAsByteStringBE x

.. code-block:: haskell

   encodeBinary :: Int64 -> ByteString
   encodeBinary x = intAsByteStringLE x

Symbiote Test Suite Instance
""""""""""""""""""""""""""""

- Topic

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Int64``.

.. code-block:: none

   "Int64"

-------------

Unsigned
^^^^^^^^

Uint8
*****

Range varies from ``0`` to ``2^8-1``

.. code-block:: haskell

   data Uint8 = 0 | 1 | ... | 2^8-2 | 2^8-1

JSON
""""

Uses standard JSON Integers

.. code-block:: haskell

   encodeJson :: Uint8 -> Json
   encodeJson x = uintAsJson x

Binary
""""""

.. code-block:: haskell

   encodeBinary :: Uint8 -> ByteString
   encodeBinary x = byteAsByteString x

Symbiote Test Suite Instance
""""""""""""""""""""""""""""

- Topic

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Uint8``.

.. code-block:: none

   "Uint8"

-------------

Uint16
******

Range varies from ``0`` to ``2^16-1``

.. code-block:: haskell

data Uint16 = 0 | 1 | ... | 2^16-2 | 2^16-1


JSON
""""

Uses standard JSON Integers

.. code-block:: haskell

   encodeJson :: Uint16 -> Json
   encodeJson x = uintAsJson x

Binary
""""""

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

.. code-block:: haskell

   encodeBinary :: Uint16 -> ByteString
   encodeBinary x = uintAsByteStringBE x

.. code-block:: haskell

   encodeBinary :: Uint16 -> ByteString
   encodeBinary x = uintAsByteStringLE x

Symbiote Test Suite Instance
""""""""""""""""""""""""""""

- Topic

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Uint16``.

.. code-block:: none

   "Uint16"


-------------

Uint32
******

Range varies from ``0`` to ``2^32-1``

.. code-block:: haskell

   data Uint32 = 0 | 1 | ... | 2^32-2 | 2^32-1


JSON
""""

Uses standard JSON Integers

.. code-block:: haskell

   encodeJson :: Uint32 -> Json
   encodeJson x = uintAsJson x


Binary
""""""

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

.. code-block:: haskell

   encodeBinary :: Uint32 -> ByteString
   encodeBinary x = uintAsByteStringBE x


.. code-block:: haskell

   encodeBinary :: Uint32 -> ByteString
   encodeBinary x = uintAsByteStringLE x

Symbiote Test Suite Instance
""""""""""""""""""""""""""""

- Topic

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Uint32``.

.. code-block:: none

   "Uint32"


-------------

Uint64
******

Range varies from ``0`` to ``2^64-1``

.. code-block:: haskell

   data Uint64 = 0 | 1 | ... | 2^64-2 | 2^64-1


JSON
""""

Uses standard JSON Integers

.. code-block:: haskell

   encodeJson :: Uint64 -> Json
   encodeJson x = uintAsJson x


Binary
""""""

There are two byte encodings for any integer larger than 8 bits - big-endian or little-endian.

.. code-block:: haskell

   encodeBinary :: Uint64 -> ByteString
   encodeBinary x = uintAsByteStringBE x


.. code-block:: haskell

   encodeBinary :: Uint64 -> ByteString
   encodeBinary x = uintAsByteStringLE x

Symbiote Test Suite Instance
""""""""""""""""""""""""""""

- Topic

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Uint64``.

.. code-block:: none

   "Uint64"


-------------

Multiple Precision
^^^^^^^^^^^^^^^^^^

Integer8
********

Arbitrary precision signed integer, implemented as (for instance) `GNU MP <https://tspiteri.gitlab.io/gmp-mpfr-sys/gmp/index.html#Top>`_, but with a max unrolled length of ``2^8`` bytes long.

JSON
""""

Uses a string encoding of the integer value, because not every platform can support very large integer
values during JSON decoding.

.. code-block:: haskell

   encodeJson :: Integer8 -> Json
   encodeJson x = stringAsJson (integerAsString x)


Binary
""""""

Performed via `cereal byte-unrolling <http://hackage.haskell.org/package/cereal-0.5.8.1/docs/src/Data.Serialize.html#line-246>`_, but with the concern that the length of unrolled bytes is an 8-bit unsigned integer.

Symbiote Test Suite Instance
""""""""""""""""""""""""""""

- Topic

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Integer8``.

.. code-block:: none

   "Integer8"

-------------

Integer16
*********

Arbitrary precision signed integer, implemented as (for instance) `GNU MP <https://tspiteri.gitlab.io/gmp-mpfr-sys/gmp/index.html#Top>`_, but with a max unrolled length of ``2^16`` bytes long.

JSON
""""

Uses a string encoding of the integer value, because not every platform can support very large integer
values during JSON decoding.

.. code-block:: haskell

   encodeJson :: Integer16 -> Json
   encodeJson x = stringAsJson (integerAsString x)


Binary
""""""

Performed via `cereal byte-unrolling <http://hackage.haskell.org/package/cereal-0.5.8.1/docs/src/Data.Serialize.html#line-246>`_, but with the concern that the length of unrolled bytes is a 16-bit unsigned integer.

Symbiote Test Suite Instance
""""""""""""""""""""""""""""

- Topic

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Integer16``.

.. code-block:: none

   "Integer16"

-------------

Integer32
*********

Arbitrary precision signed integer, implemented as (for instance) `GNU MP <https://tspiteri.gitlab.io/gmp-mpfr-sys/gmp/index.html#Top>`_, but with a max unrolled length of ``2^32`` bytes long.

JSON
""""

Uses a string encoding of the integer value, because not every platform can support very large integer
values during JSON decoding.

.. code-block:: haskell

   encodeJson :: Integer32 -> Json
   encodeJson x = stringAsJson (integerAsString x)


Binary
""""""

Performed via `cereal byte-unrolling <http://hackage.haskell.org/package/cereal-0.5.8.1/docs/src/Data.Serialize.html#line-246>`_, but with the concern that the length of unrolled bytes is a 32-bit unsigned integer.

Symbiote Test Suite Instance
""""""""""""""""""""""""""""

- Topic

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Integer32``.

.. code-block:: none

   "Integer32"

-------------

Integer64
*********

Arbitrary precision signed integer, implemented as (for instance) `GNU MP <https://tspiteri.gitlab.io/gmp-mpfr-sys/gmp/index.html#Top>`_, but with a max unrolled length of ``2^64`` bytes long.

JSON
""""

Uses a string encoding of the integer value, because not every platform can support very large integer
values during JSON decoding.

.. code-block:: haskell

   encodeJson :: Integer64 -> Json
   encodeJson x = stringAsJson (integerAsString x)


Binary
""""""

Performed via `cereal byte-unrolling <http://hackage.haskell.org/package/cereal-0.5.8.1/docs/src/Data.Serialize.html#line-246>`_, but with the concern that the length of unrolled bytes is a 64-bit unsigned integer.

Symbiote Test Suite Instance
""""""""""""""""""""""""""""

- Topic

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Integer64``.

.. code-block:: none

   "Integer64"

-------------

Natural8
********

Arbitrary precision unsigned integer, implemented as (for instance) `GNU MP <https://tspiteri.gitlab.io/gmp-mpfr-sys/gmp/index.html#Top>`_, but with a max unrolled length of ``2^8`` bytes long.

JSON
""""

Uses a string encoding of the integer value, because not every platform can support very large integer
values during JSON decoding.

.. code-block:: haskell

   encodeJson :: Natural8 -> Json
   encodeJson x = stringAsJson (naturalAsString x)


Binary
""""""

Performed via `cereal byte-unrolling <http://hackage.haskell.org/package/cereal-0.5.8.1/docs/src/Data.Serialize.html#line-306>`_, but with the concern that the length of unrolled bytes is an 8-bit unsigned integer.

Symbiote Test Suite Instance
""""""""""""""""""""""""""""

- Topic

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Natural8``.

.. code-block:: none

   "Natural8"

-------------

Natural16
*********

Arbitrary precision unsigned integer, implemented as (for instance) `GNU MP <https://tspiteri.gitlab.io/gmp-mpfr-sys/gmp/index.html#Top>`_, but with a max unrolled length of ``2^16`` bytes long.

JSON
""""

Uses a string encoding of the integer value, because not every platform can support very large integer
values during JSON decoding.

.. code-block:: haskell

   encodeJson :: Natural16 -> Json
   encodeJson x = stringAsJson (naturalAsString x)


Binary
""""""

Performed via `cereal byte-unrolling <http://hackage.haskell.org/package/cereal-0.5.8.1/docs/src/Data.Serialize.html#line-306>`_, but with the concern that the length of unrolled bytes is a 16-bit unsigned integer.

Symbiote Test Suite Instance
""""""""""""""""""""""""""""

- Topic

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Natural16``.

.. code-block:: none

   "Natural16"

-------------

Natural32
*********

Arbitrary precision unsigned integer, implemented as (for instance) `GNU MP <https://tspiteri.gitlab.io/gmp-mpfr-sys/gmp/index.html#Top>`_, but with a max unrolled length of ``2^32`` bytes long.

JSON
""""

Uses a string encoding of the integer value, because not every platform can support very large integer
values during JSON decoding.

.. code-block:: haskell

   encodeJson :: Natural32 -> Json
   encodeJson x = stringAsJson (naturalAsString x)


Binary
""""""

Performed via `cereal byte-unrolling <http://hackage.haskell.org/package/cereal-0.5.8.1/docs/src/Data.Serialize.html#line-306>`_, but with the concern that the length of unrolled bytes is a 32-bit unsigned integer.

Symbiote Test Suite Instance
""""""""""""""""""""""""""""

- Topic

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Natural32``.

.. code-block:: none

   "Natural32"

-------------

Natural64
*********

Arbitrary precision unsigned integer, implemented as (for instance) `GNU MP <https://tspiteri.gitlab.io/gmp-mpfr-sys/gmp/index.html#Top>`_, but with a max unrolled length of ``2^64`` bytes long.

JSON
""""

Uses a string encoding of the integer value, because not every platform can support very large integer
values during JSON decoding.

.. code-block:: haskell

   encodeJson :: Natural64 -> Json
   encodeJson x = stringAsJson (naturalAsString x)


Binary
""""""

Performed via `cereal byte-unrolling <http://hackage.haskell.org/package/cereal-0.5.8.1/docs/src/Data.Serialize.html#line-306>`_, but with the concern that the length of unrolled bytes is a 64-bit unsigned integer.

Symbiote Test Suite Instance
""""""""""""""""""""""""""""

- Topic

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Natural64``.

.. code-block:: none

   "Natural64"

-------------

Floating Point
~~~~~~~~~~~~~~

Float32
^^^^^^^

A binary32 implementation of `IEEE 754 <https://en.wikipedia.org/wiki/IEEE_754>`_

JSON
****

Uses standard JSON Numbers

.. code-block:: haskell

   encodeJson :: Float32 -> Json
   encodeJson x = floatAsJson x


Binary
******

There are two byte encodings for any floating point number - big-endian or little-endian.

.. code-block:: haskell

   encodeBinary :: Float32 -> ByteString
   encodeBinary x = floatAsByteStringBE x


.. code-block:: haskell

   encodeBinary :: Float32 -> ByteString
   encodeBinary x = floatAsByteStringLE x

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Float32``.

.. code-block:: none

   "Float32"


-------------

Float64
^^^^^^^

A binary64 implementation of `IEEE 754 <https://en.wikipedia.org/wiki/IEEE_754>`_

JSON
****

Uses standard JSON Numbers

.. code-block:: haskell

   encodeJson :: Float64 -> Json
   encodeJson x = floatAsJson x


Binary
******

There are two byte encodings for any floating point number - big-endian or little-endian.

.. code-block:: haskell

   encodeBinary :: Float64 -> ByteString
   encodeBinary x = floatAsByteStringBE x


.. code-block:: haskell

   encodeBinary :: Float64 -> ByteString
   encodeBinary x = floatAsByteStringLE x

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Float64``.

.. code-block:: none

   "Float64"


-------------

Scientific
^^^^^^^^^^

A `scientific notation <https://en.wikipedia.org/wiki/Scientific_notation>`_ implementation

JSON
****

Encoded as a JSON String, in canonical scientific notation - an exponential field (``*10^n``) is always
present, even when ``n == 0``, and prefixes its sign in all cases (i.e. ``9e3`` is ``9e+3``). Likewise,
the coefficient is always ``-10 < c < 10`` - no engineering notation is allowed. Furthermore,
the coefficient _never_ includes trailing zeros - i.e. ``9.230e+0`` is ``9.23e+0``. Moreover, when the value
clearly doesn't need a decimal place, it should be omitted - i.e. ``9.0e+3`` is ``9e+3``.

.. code-block:: haskell

   encodeJson :: Scientific -> Json
   encodeJson x = stringAsJson (scientificToString x)


Binary
******

Uses the same UTF8 string format as JSON, but limited to a String32_.

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Scientific``.

.. code-block:: none

   "Scientific"

-------------

Ratio
^^^^^

A (lossless) rational number implementation, by `ratios <https://en.wikipedia.org/wiki/Ratio>`_.

.. code-block:: haskell

   data Ratio a = Ratio a a


JSON
****

Encoded as a tuple of the two already encoded values

.. code-block:: haskell

   encodeJson :: Ratio Json -> Json
   encodeJson (Ratio x y) = [x,y]


Binary
******

Encoded as a tuple of the two already encoded values

.. code-block:: haskell

   encodeBinary :: Ratio ByteString -> ByteString
   encodeBinary (Ratio x y) = x ++ y

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``Ratio Int32 Int32``.

.. code-block:: none

   "Ratio"


-------------

UTF-8 Strings
~~~~~~~~~~~~~

Char
^^^^

All characters must be valid UTF-8 characters, especially with respect to surrogate codes between ``0xD800`` and
``0xDFFF`` - with respect to `RFC 3629 <https://en.wikipedia.org/wiki/UTF-8##Invalid_code_points>`_. Conversion
a 'la CESU-8 may or may not be defined with this data type.

JSON
****

Uses standard JSON Strings

.. code-block:: haskell

   encodeJson :: Char -> Json
   encodeJson x = charAsJson x


Binary
******

Encodes to a ByteString as `standard UTF-8 <https://en.wikipedia.org/wiki/UTF-8##Description>`_.

.. code-block:: haskell

   encodeBinary :: Char -> ByteString
   encodeBinary x = utf8AsByteString x

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Char``.

.. code-block:: none

   "Char"


-------------

String8
^^^^^^^

Where the length of the string is at most ``2^8`` characters long

.. code-block:: haskell

   data String8 = Vector8 Char


JSON
****

Uses standard JSON Strings

.. code-block:: haskell

   encodeJson :: String8 -> Json
   encodeJson x = stringAsJson x


Binary
******

Encodes to a ByteString as a ``Vector8`` of ``Char`` s

.. code-block:: haskell

   encodeBinary :: String8 -> ByteString
   encodeBinary x = vector8ToByteString (map utf8AsByteString (string8AsVector8 x))

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``String8``.

.. code-block:: none

   "String8"


-------------

String16
^^^^^^^^

Where the length of the string is at most ``2^16`` characters long

.. code-block:: haskell

   data String16 = Vector16 Char


JSON
****

Uses standard JSON Strings

.. code-block:: haskell

   encodeJson :: String16 -> Json
   encodeJson x = stringAsJson x


Binary
******

Encodes to a ByteString as a ``Vector16`` of ``Char`` s

.. code-block:: haskell

   encodeBinary :: String16 -> ByteString
   encodeBinary x = vector16ToByteString (map utf8AsByteString (string16AsVector16 x))

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``String16``.

.. code-block:: none

   "String16"


-------------

String32
^^^^^^^^

Where the length of the string is at most ``2^32`` characters long

.. code-block:: haskell

   data String32 = Vector32 Char


JSON
****

Uses standard JSON Strings

.. code-block:: haskell

   encodeJson :: String32 -> Json
   encodeJson x = stringAsJson x


Binary
******

Encodes to a ByteString as a ``Vector32`` of ``Char`` s

.. code-block:: haskell

   encodeBinary :: String32 -> ByteString
   encodeBinary x = vector32ToByteString (map utf8AsByteString (string32AsVector32 x))

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``String32``.

.. code-block:: none

   "String32"


-------------

String64
^^^^^^^^

Where the length of the string is at most ``2^64`` characters long

.. code-block:: haskell

   data String64 = Vector64 Char


JSON
****

Uses standard JSON Strings

.. code-block:: haskell

   encodeJson :: String64 -> Json
   encodeJson x = stringAsJson x


Binary
******

Encodes to a ByteString as a ``Vector64`` of ``Char`` s

.. code-block:: haskell

   encodeBinary :: String64 -> ByteString
   encodeBinary x = vector64ToByteString (map utf8AsByteString (string64AsVector64 x))

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``String64``.

.. code-block:: none

   "String64"


-------------

.. _casual:

Casual
------

Chronological
~~~~~~~~~~~~~

Date
^^^^

Any date system that keeps track of year, month, and day. Years are biased in the
`Common Era <https://en.wikipedia.org/wiki/Common_Era>`_, and can range from ``-2^15`` to ``2^15-1``.

.. code-block:: haskell

   data Date = Date
     (year :: Int16)
     (month :: Uint8)
     (day :: Uint8)


JSON
****

Formatted as an `ISO 8601 Calendar Date <https://en.wikipedia.org/wiki/ISO_8601#Calendar_dates>`_ / "military
date" string ``YYYYMMDD``.

.. code-block:: haskell

   encodeJson :: Date -> Json
   encodeJson x = stringAsJson (iso8601 "YYYYMMDD" x)


Binary
******

Encoded directly as one 16-bit signed integer as the year, and two bytes as the month and day. Although
there could be a way to encode a practical calendar date as 21-bits (using a 13-bit year, 4-bit month, and
5-bit day), the conversions would be considerable overhead when dealing with large amounts of date data.
And "practical" in the sense of Ancient History (3000 B.C.E.) being the limit of dating capability.

.. code-block:: haskell

   encodeByteString :: Date -> ByteString
   encodeByteString (Date year month day) =
     (intAsByteStringBE year)
       ++ (uintAsByteString month)
       ++ (uintAsByteString day)

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Date``.

.. code-block:: none

   "Date"


-------------

Time
^^^^

Any time system that keeps track of timezone, hour, minute, second, and millisecond.
Milliseconds are included because
most modern systems can emit logs with millisecond precision, and is a likely use case.

.. code-block:: haskell

   data Time = Time
     (tzhour :: Int8)
     (tzminute :: Uint8)
     (hour :: Uint8)
     (minute :: Uint8)
     (second :: Uint8)
     (millisecond :: Uint16)


JSON
****

Formatted as an `ISO 8601 Time <https://en.wikipedia.org/wiki/ISO_8601#Times>`_ string ``hhmmss.sss``.

.. code-block:: haskell

   encodeJson :: Time -> Json
   encodeJson x = stringAsJson (iso8601 "hhmmss.sss" x)


Binary
******

Encoded directly as 5 bytes for timezone, hour, minute, and second, and one 16-bit unsigned integer for
milliseconds. Although there could be a way to encode a practical time as 38-bits (5-bit hour and tzhour,
6-bit minute, tzminute and second, 10-bit millisecond), the conversions would be considerable overhead
when dealing with large amounts of time data.

.. code-block:: haskell

   encodeByteString :: Time -> ByteString
   encodeByteString
     (Time tzhour tzminute hour minute second millisecond) =
       (intAsByteString tzhour)
         ++ (uintAsByteString tzminute)
         ++ (uintAsByteString hour)
         ++ (uintAsByteString minute)
         ++ (uintAsByteString second)
         ++ (uintAsByteStringBE millisecond)

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``Time``.

.. code-block:: none

   "Time"


-------------

DateTime
^^^^^^^^

Can be represented internally as any "sane" date / time system.

.. code-block:: haskell

   data DateTime = Tuple Date Time


JSON
****

Formatted as an `ISO 8601 Combined String <https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations>`_

.. code-block:: haskell

   encodeJson :: DateTime -> Json
   encodeJson x = stringAsJson (iso8601 x)


Binary
******

Concatenation of both formats (total of 11 bytes).

.. code-block:: haskell

   encodeByteString :: DateTime -> ByteString
   encodeByteString (Tuple date time) =
     (encodeByteStringDate date)
       ++ (encodeByteStringTime time)

.. todo::

   - Intervals, Durations

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``DateTime``.

.. code-block:: none

   "DateTime"

-------------

URI-Like
~~~~~~~~

IPV4
^^^^

.. code-block:: haskell

   data IPV4 = IPV4 Uint8 Uint8 Uint8 Uint8


JSON
****

Formatted as a string to remain unambiguous

.. code-block:: haskell

   encodeJson :: IPV4 -> Json
   encodeJson x = stringAsJson (ipv4AsString x)


Binary
******

Encoded directly as 4 bytes

.. code-block:: haskell

   encodeByteString :: IPV4 -> ByteString
   encodeByteString (IPV4 a b c d) =
     (uintAsByteStringBE a)
       ++ (uintAsByteStringBE b)
       ++ (uintAsByteStringBE c)
       ++ (uintAsByteStringBE d)

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``IPV4``.

.. code-block:: none

   "IPV4"


-------------

IPV6
^^^^

.. code-block:: haskell

   data IPV6 =
     IPV6
       Uint16 Uint16 Uint16 Uint16
       Uint16 Uint16 Uint16 Uint16


JSON
****

Formatted as a string to remain unambiguous

.. code-block:: haskell

   encodeJson :: IPV6 -> Json
   encodeJson x = stringAsJson (ipv6AsString x)


Binary
******

Encoded directly as 16 bytes

.. code-block:: haskell

   encodeByteString :: IPV6 -> ByteString
   encodeByteString (IPV6 a b c d e f g h) =
     (uintAsByteStringBE a)
       ++ (uintAsByteStringBE b)
       ++ (uintAsByteStringBE c)
       ++ (uintAsByteStringBE d)
       ++ (uintAsByteStringBE e)
       ++ (uintAsByteStringBE f)
       ++ (uintAsByteStringBE g)
       ++ (uintAsByteStringBE h)

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``IPV6``.

.. code-block:: none

   "IPV6"


-------------

URI
^^^

Should be a valid `URI <https://en.wikipedia.org/wiki/Uniform_Resource_Identifier#Generic_syntax>`_
with `Percent Encoding <https://en.wikipedia.org/wiki/Percent-encoding>`_ for all
reserved, non-valid, and UTF-8 characters in their appropriate components in the URI, while the query may
have ``x-www-form-urlencoded`` data.

JSON
****

Formatted as its string representation

.. code-block:: haskell

   encodeJson :: URI -> Json
   encodeJson x = stringAsJson (uriAsString x)


Binary
******

Encoded as a UTF-8 String32_ (though there are only ASCII characters allowed) - other implementations of ``URI8`` etc may
exist in a future version.

.. code-block:: haskell

   encodeByteString :: URI -> ByteString
   encodeByteString x = utf8AsByteString (uriAsString x)

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``URI``.

.. code-block:: none

   "URI"


-------------

Email Address
^^^^^^^^^^^^^

Should be represented in a vaild ASCII Email Address format, as per `Wikipedia <https://en.wikipedia.org/wiki/Email_address#Syntax>`_ / `RFC 5322 <https://tools.ietf.org/html/rfc5322#section-3.4.1>`_.

JSON
****

Formatted as the string representation

.. code-block:: haskell

   encodeJson :: EmailAddress -> Json
   encodeJson x = stringAsJson (emailAddressAsString x)


Binary
******

Encoded as a UTF-8 ``String16`` (though there are only ASCII characters allowed)

.. code-block:: haskell

   encodeByteString :: EmailAddress -> ByteString
   encodeByteString x = utf8AsByteString (emailAddressAsString x)

.. todo::

    - International Email Addresses a 'la https://en.wikipedia.org/wiki/International_email

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed solely as ``EmailAddress``.

.. code-block:: none

   "EmailAddress"

-------------

.. _primitivecomposites:

Primitive Composites
--------------------

Collections
~~~~~~~~~~~

Array
^^^^^

A size-indexed array of homogeneous data.

.. code-block:: haskell

   data Array (n :: Nat) a where
     Nil :: Array 0 a
     Cons :: a -> Array n a -> Array (n + 1) a


JSON
****

Uses standard JSON Arrays

.. code-block:: haskell

   encodeJson :: Array n Json -> Json
   encodeJson x = arrayAsJson x


Binary
******

Ommits a size parameter, because the size is encoded in the type signature.

.. code-block:: haskell

   encodeBinary :: Array n ByteString -> ByteString
   encodeBinary x = case x of
     Nil -> emptyByteString
     Cons y ys -> y ++ (encodeBinary ys)

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``Array 20 Int32`` (length of 20 ``Int32`` s).

.. code-block:: none

   "Array"


-------------

Vector8
^^^^^^^

A dynamically sized array that limits the max size to ``2^8`` elements

JSON
****

Uses standard JSON Arrays

.. code-block:: haskell

   encodeJson :: Vector8 Json -> Json
   encodeJson x = arrayAsJson x


Binary
******

Prefixes the length of the array as a 8-bit unsigned integer, big-endian, before concatenating all contents.

.. code-block:: haskell

   encodeBinary :: Vector8 ByteString -> ByteString
   encodeBinary x = (uintAsByteStringBE l) ++ (concatVector8 x)

Symbiote Test Suite Instance
****************************

Topic
*****

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``Vector8 Int32``.

.. code-block:: none

   "Vector8"


-------------

Vector16
^^^^^^^^

A dynamically sized array that limits the max size to ``2^16`` elements

JSON
****

Uses standard JSON Arrays

.. code-block:: haskell

   encodeJson :: Vector16 Json -> Json
   encodeJson x = arrayAsJson x


Binary
******

Prefixes the length of the array as a 16-bit unsigned integer, big-endian, before concatenating all contents.

.. code-block:: haskell

   encodeBinary :: Vector16 ByteString -> ByteString
   encodeBinary x = (uintAsByteStringBE l) ++ (concatVector16 x)

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``Vector16 Int32``.

.. code-block:: none

   "Vector16"


-------------

Vector32
^^^^^^^^

A dynamically sized array that limits the max size to ``2^32`` elements

JSON
****

Uses standard JSON Arrays

.. code-block:: haskell

   encodeJson :: Vector32 Json -> Json
   encodeJson x = arrayAsJson x


Binary
******

Prefixes the length of the array as a 32-bit unsigned integer, big-endian, before concatenating all contents.

.. code-block:: haskell

   encodeBinary :: Vector32 ByteString -> ByteString
   encodeBinary x = (uintAsByteStringBE l) ++ (concatVector32 x)

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``Vector32 Int32``.

.. code-block:: none

   "Vector32"


-------------

Vector64
^^^^^^^^

A dynamically sized array that limits the max size to ``2^64`` elements

JSON
****

Uses standard JSON Arrays

.. code-block:: haskell

   encodeJson :: Vector64 Json -> Json
   encodeJson x = arrayAsJson x


Binary
******

Prefixes the length of the array as a 64-bit unsigned integer, big-endian, before concatenating all contents.

.. code-block:: haskell

   encodeBinary :: Vector64 ByteString -> ByteString
   encodeBinary x = (uintAsByteStringBE l) ++ (concatVector64 x)

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``Vector64 Int32``.

.. code-block:: none

   "Vector64"


-------------

Maybe
~~~~~

Standard option type

.. code-block:: haskell

   data Maybe a = Nothing | Maybe a


JSON
^^^^

Uses standard JSON ``null`` if ``Nothing``, otherwise just use its JSON - leverages backtracking

.. code-block:: haskell

   encodeJson :: Maybe Json -> Json
   encodeJson x = case x of
     Nothing -> nullJson
     Just y -> y


Binary
^^^^^^

Use a prefix byte flag to avoid backtracking

.. code-block:: haskell

   encodeBinary :: Maybe ByteString -> ByteString
   encodeBinary x = case x of
     Nothing -> byteAsByteString 0
     Just y -> (byteAsByteString 1) ++ y

Symbiote Test Suite Instance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Topic
*****

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``Maybe Int32``.

.. code-block:: none

   "Maybe"


-------------

Tuple
~~~~~

.. code-block:: haskell

   data Tuple a b = Tuple a b


JSON
^^^^

Uses a standard JSON Array to hold the two elements

.. code-block:: haskell

   encodeJson :: Tuple Json Json -> Json
   encodeJson (Tuple x y) = [x,y]


Binary
^^^^^^

Is equivalent to an array of size 2, therefore avoids a size prefix

.. code-block:: haskell

   encodeBinary :: Tuple ByteString ByteString -> ByteString
   encodeBinary (Tuple x y) = x ++ y

Symbiote Test Suite Instance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Topic
*****

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``Tuple Int32 Int32``.

.. code-block:: none

   "Tuple"


-------------

Either
~~~~~~

.. code-block:: haskell

   data Either a b = Left a | Right b


JSON
^^^^

Flags each case with a unique object key

.. code-block:: haskell

   encodeJson :: Either Json Json -> Json
   encodeJson x = case x of
     Left y -> {"l": y}
     Right z -> {"r": z}


Binary
^^^^^^

Flags each case with a byte prefix

.. code-block:: haskell

   encodeBinary :: Either ByteString ByteString -> ByteString
   encodeBinary x = case x of
     Left y -> (byteAsByteString 0) ++ y
     Right z -> (byteAsByteString 1) ++ z

Symbiote Test Suite Instance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Topic
*****

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``Either Int32 Int32``.

.. code-block:: none

   "Either"


-------------

.. _sophisticatedcomposites:

Sophisticated Composites
------------------------

Mappings
~~~~~~~~

StringMap8
^^^^^^^^^^

Mapping where ``String8`` s are the keys - can be implemented as a hash-map internally, or as a JSON object
as the case with JavaScript.

JSON
****

Serialized as a JSON object

.. code-block:: haskell

   encodeJson :: StringMap8 Json -> Json
   encodeJson x = stringMap8AsJson x


Binary
******

Encodes as a dynamically sized array of key-value tuples, where the size is a 8-bit unsigned integer.

.. code-block:: haskell

   encodeBinary :: StringMap8 ByteString -> ByteString
   encodeBinary x = concatVector8 (map tupleToByteString (stringMap8AsVector8 x))
     where
       tupleToByteString :: Tuple String ByteString -> ByteString
       tupleToByteString (Tuple k v) = (encodeByteString k) ++ v

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``StringMap8 Int32``.

.. code-block:: none

   "StringMap8"


-------------

StringMap16
^^^^^^^^^^^

Mapping where ``String16`` s are the keys - can be implemented as a hash-map internally, or as a JSON object
as the case with JavaScript.

JSON
****

Serialized as a JSON object

.. code-block:: haskell

   encodeJson :: StringMap16 Json -> Json
   encodeJson x = stringMap16AsJson x


Binary
******

Encodes as a dynamically sized array of key-value tuples, where the size is a 16-bit unsigned integer.

.. code-block:: haskell

   encodeBinary :: StringMap16 ByteString -> ByteString
   encodeBinary x = concatVector16 (map tupleToByteString (stringMap16AsVector16 x))
     where
       tupleToByteString :: Tuple String ByteString -> ByteString
       tupleToByteString (Tuple k v) = (encodeByteString k) ++ v

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``StringMap16 Int32``.

.. code-block:: none

   "StringMap16"


-------------

StringMap32
^^^^^^^^^^^

Mapping where String32_ s are the keys - can be implemented as a hash-map internally, or as a JSON object
as the case with JavaScript.

JSON
****

Serialized as a JSON object

.. code-block:: haskell

   encodeJson :: StringMap32 Json -> Json
   encodeJson x = stringMap32AsJson x


Binary
******

Encodes as a dynamically sized array of key-value tuples, where the size is a 32-bit unsigned integer.

.. code-block:: haskell

   encodeBinary :: StringMap32 ByteString -> ByteString
   encodeBinary x = concatVector32 (map tupleToByteString (stringMap32AsVector32 x))
     where
       tupleToByteString :: Tuple String ByteString -> ByteString
       tupleToByteString (Tuple k v) = (encodeByteString k) ++ v

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``StringMap32 Int32``.

.. code-block:: none

   "StringMap32"


-------------

StringMap64
^^^^^^^^^^^

Mapping where ``String64`` s are the keys - can be implemented as a hash-map internally, or as a JSON object
as the case with JavaScript.

JSON
****

Serialized as a JSON object

.. code-block:: haskell

   encodeJson :: StringMap64 Json -> Json
   encodeJson x = stringMap64AsJson x


Binary
******

Encodes as a dynamically sized array of key-value tuples, where the size is a 64-bit unsigned integer.

.. code-block:: haskell

   encodeBinary :: StringMap64 ByteString -> ByteString
   encodeBinary x = concatVector64 (map tupleToByteString (stringMap64AsVector64 x))
     where
       tupleToByteString :: Tuple String ByteString -> ByteString
       tupleToByteString (Tuple k v) = (encodeByteString k) ++ v

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``StringMap64 Int32``.

.. code-block:: none

   "StringMap64"




-------------

Map8
^^^^

Polymorphic mapping - can be implemented any way: B-Tree, or unordered - serialization does not restrict
the implementation.

JSON
****

Serialized as an array of arrays / tuples.

.. code-block:: haskell

   encodeJson :: Map8 Json Json -> Json
   encodeJson x = map tupleToJson (map8AsVector8 x)
     where
       tupleToJson :: Tuple Json Json -> Json
       tupleToJson (Tuple k v) = [k,v]


Binary
******

Encodes as a dynamically sized array of key-value tuples, where the size is a 8-bit unsigned integer.

.. code-block:: haskell

   encodeBinary :: Map8 ByteString ByteString -> ByteString
   encodeBinary x = concatVector8 (map tupleToByteString (map8AsVector8 x))
     where
       tupleToByteString :: Tuple ByteString ByteString -> ByteString
       tupleToByteString (Tuple k v) = k ++ v

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``Map8 Int32 Int32``.

.. code-block:: none

   "Map8"


-------------

Map16
^^^^^

Polymorphic mapping - can be implemented any way: B-Tree, or unordered - serialization does not restrict
the implementation.

JSON
****

Serialized as an array of arrays / tuples.

.. code-block:: haskell

   encodeJson :: Map16 Json Json -> Json
   encodeJson x = map tupleToJson (map16AsVector16 x)
     where
       tupleToJson :: Tuple Json Json -> Json
       tupleToJson (Tuple k v) = [k,v]


Binary
******

Encodes as a dynamically sized array of key-value tuples, where the size is a 16-bit unsigned integer.

.. code-block:: haskell

   encodeBinary :: Map16 ByteString ByteString -> ByteString
   encodeBinary x = concatVector16 (map tupleToByteString (map16AsVector16 x))
     where
       tupleToByteString :: Tuple ByteString ByteString -> ByteString
       tupleToByteString (Tuple k v) = k ++ v

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``Map16 Int32 Int32``.

.. code-block:: none

   "Map16"


-------------

Map32
^^^^^

Polymorphic mapping - can be implemented any way: B-Tree, or unordered - serialization does not restrict
the implementation.

JSON
****

Serialized as an array of arrays / tuples.

.. code-block:: haskell

   encodeJson :: Map32 Json Json -> Json
   encodeJson x = map tupleToJson (map32AsVector32 x)
     where
       tupleToJson :: Tuple Json Json -> Json
       tupleToJson (Tuple k v) = [k,v]


Binary
******

Encodes as a dynamically sized array of key-value tuples, where the size is a 32-bit unsigned integer.

.. code-block:: haskell

   encodeBinary :: Map32 ByteString ByteString -> ByteString
   encodeBinary x = concatVector32 (map tupleToByteString (map32AsVector32 x))
     where
       tupleToByteString :: Tuple ByteString ByteString -> ByteString
       tupleToByteString (Tuple k v) = k ++ v

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``Map32 Int32 Int32``.

.. code-block:: none

   "Map32"


-------------

Map64
^^^^^

Polymorphic mapping - can be implemented any way: B-Tree, or unordered - serialization does not restrict
the implementation.

JSON
****

Serialized as an array of arrays / tuples.

.. code-block:: haskell

   encodeJson :: Map64 Json Json -> Json
   encodeJson x = map tupleToJson (map64AsVector64 x)
     where
       tupleToJson :: Tuple Json Json -> Json
       tupleToJson (Tuple k v) = [k,v]


Binary
******

Encodes as a dynamically sized array of key-value tuples, where the size is a 64-bit unsigned integer.

.. code-block:: haskell

   encodeBinary :: Map64 ByteString ByteString -> ByteString
   encodeBinary x = concatVector64 (map tupleToByteString (map64AsVector64 x))
     where
       tupleToByteString :: Tuple ByteString ByteString -> ByteString
       tupleToByteString (Tuple k v) = k ++ v

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``Map64 Int32 Int32``.

.. code-block:: none

   "Map64"



-------------

Tries
~~~~~

StringTrie8
^^^^^^^^^^^

Recursive ``StringMap8``, with values along the way.

.. code-block:: haskell

   data StringTrie8 a = StringMap8 (Tuple (Maybe a) (StringTrie8 a))


JSON
****

Uses a standard JSON Object as the key index

.. code-block:: haskell

   encodeJson :: StringTrie8 Json -> Json
   encodeJson x = stringMap8AsObject (map tupleToJson x)
     where
       tupleToJson :: Tuple (Maybe Json) (StringTrie8 Json) -> Json
       tupleToJson (Tuple v y) = [maybeToJson v, encodeJson y]


Binary
******

Encoded as a series of dynamically sized arrays - uses composite ``encodeByteString`` instances for each level.

.. code-block:: haskell

   encodeByteString :: StringTrie8 ByteString -> ByteString
   encodeByteString x = encodeByteStringVector8 (stringMap8AsVector8 (map tupleToByteString x))
     where
       tupleToByteString :: Tuple (Maybe ByteString) (StringTrie8 ByteString) -> ByteString
       tupleToByteString (Tuple v y) = (maybeToByteString v) ++ (encodeByteString y)

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``StringTrie8 Int32``.

.. code-block:: none

   "StringTrie8"


-------------

StringTrie16
^^^^^^^^^^^^

Recursive ``StringMap16``, with values along the way.

.. code-block:: haskell

   data StringTrie16 a = StringMap16 (Tuple (Maybe a) (StringTrie16 a))


JSON
****

Uses a standard JSON Object as the key index

.. code-block:: haskell

   encodeJson :: StringTrie16 Json -> Json
   encodeJson x = stringMap16AsObject (map tupleToJson x)
     where
       tupleToJson :: Tuple (Maybe Json) (StringTrie16 Json) -> Json
       tupleToJson (Tuple v y) = [maybeToJson v, encodeJson y]


Binary
******

Encoded as a series of dynamically sized arrays - uses composite ``encodeByteString`` instances for each level.

.. code-block:: haskell

   encodeByteString :: StringTrie16 ByteString -> ByteString
   encodeByteString x = encodeByteStringVector16 (stringMap16AsVector16 (map tupleToByteString x))
     where
       tupleToByteString :: Tuple (Maybe ByteString) (StringTrie16 ByteString) -> ByteString
       tupleToByteString (Tuple v y) = (maybeToByteString v) ++ (encodeByteString y)

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``StringTrie16 Int32``.

.. code-block:: none

   "StringTrie16"


-------------

StringTrie32
^^^^^^^^^^^^

Recursive ``StringMap32``, with values along the way.

.. code-block:: haskell

   data StringTrie32 a = StringMap32 (Tuple (Maybe a) (StringTrie32 a))


JSON
****

Uses a standard JSON Object as the key index

.. code-block:: haskell

   encodeJson :: StringTrie32 Json -> Json
   encodeJson x = stringMap32AsObject (map tupleToJson x)
     where
       tupleToJson :: Tuple (Maybe Json) (StringTrie32 Json) -> Json
       tupleToJson (Tuple v y) = [maybeToJson v, encodeJson y]


Binary
******

Encoded as a series of dynamically sized arrays - uses composite ``encodeByteString`` instances for each level.

.. code-block:: haskell

   encodeByteString :: StringTrie32 ByteString -> ByteString
   encodeByteString x = encodeByteStringVector32 (stringMap32AsVector32 (map tupleToByteString x))
     where
       tupleToByteString :: Tuple (Maybe ByteString) (StringTrie32 ByteString) -> ByteString
       tupleToByteString (Tuple v y) = (maybeToByteString v) ++ (encodeByteString y)

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``StringTrie32 Int32``.

.. code-block:: none

   "StringTrie32"


-------------

StringTrie64
^^^^^^^^^^^^

Recursive ``StringMap64``, with values along the way.

.. code-block:: haskell

   data StringTrie64 a = StringMap64 (Tuple (Maybe a) (StringTrie64 a))


JSON
****

Uses a standard JSON Object as the key index

.. code-block:: haskell

   encodeJson :: StringTrie64 Json -> Json
   encodeJson x = stringMap64AsObject (map tupleToJson x)
     where
       tupleToJson :: Tuple (Maybe Json) (StringTrie64 Json) -> Json
       tupleToJson (Tuple v y) = [maybeToJson v, encodeJson y]


Binary
******

Encoded as a series of dynamically sized arrays - uses composite ``encodeByteString`` instances for each level.

.. code-block:: haskell

   encodeByteString :: StringTrie64 ByteString -> ByteString
   encodeByteString x = encodeByteStringVector64 (stringMap64AsVector64 (map tupleToByteString x))
     where
       tupleToByteString :: Tuple (Maybe ByteString) (StringTrie64 ByteString) -> ByteString
       tupleToByteString (Tuple v y) = (maybeToByteString v) ++ (encodeByteString y)

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``StringTrie64 Int32``.

.. code-block:: none

   "StringTrie64"


-------------

Trie8
^^^^^

Recursive ``Map8``, with values along the way.

.. code-block:: haskell

   data Trie8 k a = Map8 k (Tuple (Maybe a) (Trie8 k a))


JSON
****

Uses nested Arrays

.. code-block:: haskell

   encodeJson :: Trie8 Json Json -> Json
   encodeJson x = map8AsVector8 (map tupleToJson x)
     where
       tupleToJson :: Tuple (Maybe Json) (Trie8 Json Json) -> Json
       tupleToJson (Tuple v y) = [maybeToJson v, encodeJson y]


Binary
******

Encoded as a series of dynamically sized arrays - uses composite ``encodeByteString`` instances for each level.

.. code-block:: haskell

   encodeByteString :: Trie8 ByteString ByteString -> ByteString
   encodeByteString x = encodeByteStringVector8 (map8AsVector8 (map tupleToByteString x))
     where
       tupleToByteString :: Tuple (Maybe ByteString) (Trie8 ByteString ByteString) -> ByteString
       tupleToByteString (Tuple v y) = (maybeToByteString v) ++ (encodeByteString y)

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``Trie8 Int32 Int32``.

.. code-block:: none

   "Trie8"


-------------

Trie16
^^^^^^

Recursive ``Map16``, with values along the way.

.. code-block:: haskell

   data Trie16 k a = Map16 k (Tuple (Maybe a) (Trie16 k a))


JSON
****

Uses nested Arrays

.. code-block:: haskell

   encodeJson :: Trie16 Json Json -> Json
   encodeJson x = map16AsVector16 (map tupleToJson x)
     where
       tupleToJson :: Tuple (Maybe Json) (Trie16 Json Json) -> Json
       tupleToJson (Tuple v y) = [maybeToJson v, encodeJson y]


Binary
******

Encoded as a series of dynamically sized arrays - uses composite ``encodeByteString`` instances for each level.

.. code-block:: haskell

   encodeByteString :: Trie16 ByteString ByteString -> ByteString
   encodeByteString x = encodeByteStringVector16 (map16AsVector16 (map tupleToByteString x))
     where
       tupleToByteString :: Tuple (Maybe ByteString) (Trie16 ByteString ByteString) -> ByteString
       tupleToByteString (Tuple v y) = (maybeToByteString v) ++ (encodeByteString y)

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``Trie16 Int32 Int32``.

.. code-block:: none

   "Trie16"


-------------

Trie32
^^^^^^

Recursive ``Map32``, with values along the way.

.. code-block:: haskell

   data Trie32 k a = Map32 k (Tuple (Maybe a) (Trie32 k a))


JSON
****

Uses nested Arrays

.. code-block:: haskell

   encodeJson :: Trie32 Json Json -> Json
   encodeJson x = map32AsVector32 (map tupleToJson x)
     where
       tupleToJson :: Tuple (Maybe Json) (Trie32 Json Json) -> Json
       tupleToJson (Tuple v y) = [maybeToJson v, encodeJson y]


Binary
******

Encoded as a series of dynamically sized arrays - uses composite ``encodeByteString`` instances for each level.

.. code-block:: haskell

   encodeByteString :: Trie32 ByteString ByteString -> ByteString
   encodeByteString x = encodeByteStringVector32 (map32AsVector32 (map tupleToByteString x))
     where
       tupleToByteString :: Tuple (Maybe ByteString) (Trie32 ByteString ByteString) -> ByteString
       tupleToByteString (Tuple v y) = (maybeToByteString v) ++ (encodeByteString y)

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``Trie32 Int32 Int32``.

.. code-block:: none

   "Trie32"


-------------

Trie64
^^^^^^

Recursive ``Map64``, with values along the way.

.. code-block:: haskell

   data Trie64 k a = Map64 k (Tuple (Maybe a) (Trie64 k a))


JSON
****

Uses nested Arrays

.. code-block:: haskell

   encodeJson :: Trie64 Json Json -> Json
   encodeJson x = map64AsVector64 (map tupleToJson x)
     where
       tupleToJson :: Tuple (Maybe Json) (Trie64 Json Json) -> Json
       tupleToJson (Tuple v y) = [maybeToJson v, encodeJson y]


Binary
******

Encoded as a series of dynamically sized arrays - uses composite ``encodeByteString`` instances for each level.

.. code-block:: haskell

   encodeByteString :: Trie64 ByteString ByteString -> ByteString
   encodeByteString x = encodeByteStringVector64 (map64AsVector64 (map tupleToByteString x))
     where
       tupleToByteString :: Tuple (Maybe ByteString) (Trie64 ByteString ByteString) -> ByteString
       tupleToByteString (Tuple v y) = (maybeToByteString v) ++ (encodeByteString y)

Symbiote Test Suite Instance
****************************

Topic
"""""

String literal :ref:`Topic <topic>` used in the :ref:`Symbiote Test Suite <testsuite>` for this data type.
Composed as ``Trie64 Int32 Int32``.

.. code-block:: none

   "Trie64"

