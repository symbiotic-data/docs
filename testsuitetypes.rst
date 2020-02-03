Symbiote Test Suite Definitions
===============================

Topic
-----

A ``Topic`` is just an alias for a String, whatever that is for your platform. When serialized, it will be
UTF-8 encoded.

.. code-block:: haskell


   data Topic = Topic String


Generate
~~~~~~~~

Generating Unicode strings can be tricky business. It is common for operating systems to treat surrogate
characters as special, and should therefore be avoided. See `this Wikipedia article on UTF-16 <https://en.wikipedia.org/wiki/UTF-16#U+D800_to_U+DFFF>`_ (which is what JavaScript uses under-the-hood) for more details.


.. code-block:: haskell

   generate :: Gen Topic
   generate = do
     characters <- arrayOf (arbitraryChar `suchThat` isNotSurrogate)
     return (Topic (arrayToString characters))


JSON
~~~~

``Topic`` values are encoded as a plain JSON string.


.. code-block:: haskell

   encodeJson :: Topic -> Json
   encodeJson (Topic t) = stringAsJson t


Binary
~~~~~~

This protocol is required to support JavaScript, which does not support 64-bit integers natively. Therefore,
the encoding mechanism used here will do a standard UTF-8 encoding with a 32-bit size index of the bytestring.


.. code-block:: haskell

   encodeBinary :: Topic -> ByteString
   encodeBinary (Topic t) =
     (intAsByteStringBE (length t :: Int32))
       ++ (stringAsByteString t)


Size
----

JavaScript does not support 64-bit integers, so it is our lowest-common-denominator for this test suite,
and we should use 32-bit integers whenever we need an ``Int``.


.. code-block:: haskell

   data Size = Size Int32


Generate
~~~~~~~~


.. code-block:: haskell

   generate :: Gen Size
   generate = do
     size <- between 0 (2^31 - 1)
     return (Size size)


JSON
~~~~

JavaScript can handle 32-bit integers, so we can directly use our ``Size`` in a JSON document.


.. code-block:: haskell

   encodeJson :: Size -> Json
   encodeJson (Size s) = intAsJson s


Binary
~~~~~~


.. code-block:: haskell

   encodeBinary :: Size -> ByteString
   encodeBinary (Size s) = intAsByteStringBE s


AvailableTopics
---------------

In a platform's implementation, an ``AvailableTopics`` is just a mapping from `Topic`s to `Size`s -
this could be a HashMap, a B-tree map, or a JSON object.


.. code-block:: haskell

   data AvailableTopics = AvailableTopics (Map Topic Size)


Generate
~~~~~~~~


.. code-block:: haskell

   generate :: Gen AvailableTopics
   generate = do
     pairs <- arrayOf (pairOf arbitraryTopic arbitrarySize)
     return (AvailableTopics (arrayToMap pairs))


JSON
~~~~

This data type is the same as a JSON object of integers, so we'll use that for its JSON encoding.


.. code-block:: haskell

   encodeJson :: AvailableTopics -> Json
   encodeJson (AvailableTopics ts) = mapAsJsonObject ts


Binary
~~~~~~

A ``Topic`` tells us how many bytes it uses in its first 32-bit value, while ``Size`` is always 4 bytes
because its a 32-bit integer. Therefore, a pair of a ``Topic`` and ``Size`` can be stored right next
to each other, and the only thing we have have to worry about is storing *how many* pairs there are.


.. code-block:: haskell

   encodeBinary :: AvailableTopics -> ByteString
   encodeBinary (AvailableTopics ts) =
     (intAsByteStringBE (length ts :: Int32))
       ++ (arrayAsByteString (map pairToByteString (mapToArray ts)))
     where
       pairToByteString :: (Topic, Size) -> ByteString
       pairToByteString (t,s) = (encodeBinary t) ++ (encodeBinary s)


Generating
----------

A ``Generating`` message is sent by the peer doing the generating, and received by the peer operating.
There are a few options to consider --- it's an enumerated type. Furthermore, it's defined here generically
over its serialized type, but the idea is the same.


.. code-block:: haskell

   data Generating target
     = Generated (value :: target) (operation :: target)
     | BadResult (result :: target)
     | YourTurn
     | ImFinished
     | NoParseOperated (result :: target)


Note that we are not using type ``T`` or ``OperationsOnT`` here --- we may have many different types to deal with,
and therefore can't be constrained to one universal type. However, there is only one ``Target`` type over
the ``Socket`` we communicate over, and can therefore be defined against that.

JSON
~~~~

This type's enumerated options are distinguished by varying keys in a JSON object.


.. code-block:: haskell

   encodeJson :: Generating Json -> Json
   encodeJson x = case x of
     Generated value operation -> {"generated": {"value": value, "operation": operation}}
     BadResult result -> {"badResult": result}
     YourTurn -> stringAsJson "yourTurn"
     ImFinished -> stringAsJson "imFinished"
     NoParseOperated result -> {"noParseOperated": result}


Binary
~~~~~~

The different enumerated options will be distinguished by a varying initial byte flag.


.. code-block:: haskell

   encodeBinary :: Generating ByteString -> ByteString
   encodeBinary x = case x of
     Generated value operation ->
       (byteAsByteString 0)
         ++ (byteStringWithLength value)
         ++ (byteStringWithLength operation)
     BadResult result ->
       (byteAsByteString 1)
         ++ (byteStringWithLength result)
     YourTurn -> byteAsByteString 2
     ImFinished -> byteAsByteString 3
     NoParseOperated result ->
       (byteAsByteString 4)
         ++ (byteStringWithLength result)


Where ``byteStringWithLength`` prefixes the `ByteString`'s byte-length as a 32-bit integer.


Operating
---------

An ``Operating`` message is sent by the peer doing the operating, and received by the peer that generated.


.. code-block:: haskell

   data Operating target
     = Operated (result :: target)
     | NoParseValue (value :: target)
     | NoParseOperation (operation :: target)


JSON
~~~~


.. code-block:: haskell

   encodeJson :: Operating Json -> Json
   encodeJson x = case x of
     Operated result -> {"operated": result}
     NoParseValue value -> {"noParseValue": value}
     NoParseOperation operation -> {"noParseOperation": operation}


Binary
~~~~~~


.. code-block:: haskell

   encodeBinary :: Operating ByteString -> ByteString
   encodeBinary x = case x of
     Operated result ->
       (byteAsByteString 0)
         ++ (byteStringWithLength result)
     NoParseValue value ->
       (byteAsByteString 1)
         ++ (byteStringWithLength value)
     NoParseOperation operation ->
       (byteAsByteString 2)
         ++ (byteStringWithLength operation)


Where ``byteStringWithLength`` prefixes the `ByteString`'s byte-length as a 32-bit integer.

First
-----

These are the messages sent by the ``First`` party in the protocol.


.. code-block:: haskell

   data First target
     = Topics AvailableTopics
     | BadStartSubset
     | FirstGenerating Topic (Generating target)
     | FirstOperating Topic (Operating target)


JSON
~~~~


.. code-block:: haskell

   encodeJson :: First Json -> Json
   encodeJson x = case x of
     Topics availableTopics ->
       {"availableTopics": encodeJson availableTopics}
     BadStartSubset ->
       "badStartSubset"
     FirstGenerating topic generating ->
       { "firstGenerating":
         { "topic": encodeJson topic
         , "generating": encodeJson generating
         }
       }
     FirstOperating topic operating ->
       { "firstOperating":
         { "topic": encodeJson topic
         , "operating": encodeJson operating
         }
       }


Binary
~~~~~~


.. code-block:: haskell

   encodeBinary :: First ByteString -> ByteString
   encodeBinary x = case x of
     Topics availableTopics ->
       (byteAsByteString 0)
         ++ (encodeBinary availableTopics)
     BadStartSubset ->
       byteAsByteString 1
     FirstGenerating topic generating ->
       (byteAsByteString 2)
         ++ (encodeBinary topic)
         ++ (encodeBinary generating)
     FirstOperating topic operating ->
       (byteAsByteString 3)
         ++ (encodeBinary topic)
         ++ (encodeBinary operating)


Second
------

These are the messages sent by the ``Second`` party in the protocol.


.. code-block:: haskell

   data Second target
     = BadTopics AvailableTopics
     | Start (Set Topic)
     | SecondOperating (Operating target)
     | SecondGenerating (Generating target)


JSON
~~~~


.. code-block:: haskell

   encodeJson :: Second Json -> Json
   encodeJson x = case x of
     BadTopics availableTopics ->
       {"badTopics": encodeJson availableTopics}
     Start topics ->
       {"start": encodeJson topics}
     SecondOperating topic operating ->
       { "secondOperating":
         { "topic": encodeJson topic
         , "operating": encodeJson operating
         }
       }
     SecondGenerating topic generating ->
       { "secondGenerating":
         { "topic": encodeJson topic
         , "generating": encodeJson generating
         }
       }


Binary
~~~~~~


.. code-block:: haskell

   encodeBinary :: Second ByteString -> ByteString
   encodeBinary x = case x of
     BadTopics availableTopics ->
       (byteAsByteString 0)
         ++ (encodeBinary availableTopics)
     Start topics ->
       (byteAsByteString 1)
         ++ (encodeBinary topics)
     SecondOperating topic operating ->
       (byteAsByteString 2)
         ++ (encodeBinary topic)
         ++ (encodeBinary operating)
     SecondGenerating topic generating ->
       (byteAsByteString 3)
         ++ (encodeBinary topic)
         ++ (encodeBinary generating)

