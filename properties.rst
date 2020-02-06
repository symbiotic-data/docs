.. _properties:

Algebraic Properties
====================

The various data types defined in Symbiotic-Data form various algebraic objects, and by extension support
certain properties over their operations.

Semigroup
---------

Semigroups have one operation defined on it, ``append``, and it should be `associative <https://en.wikipedia.org/wiki/Associative_property>`_.

.. code-block:: haskell

   class Semigroup a where
     append :: a -> a -> a

   isAssociative :: Semigroup a
                 => a -> a -> a -> Bool
   isAssociative x y z =
     append (append x y) z == append x (append y z)

Operations
~~~~~~~~~~

.. code-block:: haskell

   data SemigroupOperation a
     = SemigroupAssociative a a

   perform :: Semigroup a
           => SemigroupOperation a -> a -> Bool
   perform op x = case op of
     SemigroupAssociative y z ->
       isAssociative x y z

JSON
****

.. code-block:: haskell

   encodeJson :: SemigroupOperation Json -> Json
   encodeJson op = case op of
     SemigroupAssociative y z ->
       {"associative": {"y": y, "z": z}}

Binary
******

.. code-block:: haskell

   encodeBinary :: SemigroupOperation ByteString -> ByteString
   encodeBinary op = case op of
     SemigroupAssociative y z -> y ++ z
