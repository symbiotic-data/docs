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

   isAssociative :: Semigroup a =>
     a -> a -> a -> Bool
   isAssociative x y z =
     append (append x y) z == append x (append y z)

Operations
~~~~~~~~~~

.. code-block:: haskell

   data SemigroupOperation a
     = SemigroupAssociative a a

   performSemigroup :: Semigroup a =>
     SemigroupOperation a -> a -> Bool
   performSemigroup op x = case op of
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

---------------

Monoid
------

Monoids are a superclass of Semigroup_ and inherit all of their faculties. They have one additional operation
defined on it, ``mempty``, and it should be the `identity element <https://en.wikipedia.org/wiki/Identity_element>`_
over ``append``.

.. code-block:: haskell

   class Semigroup a => Monoid a where
     mempty :: a

   isLeftIdentity :: Monoid a =>
     a -> Bool
   isLeftIdentity x = append mempty x == x

   isRightIdentity :: Monoid a =>
     a -> Bool
   isRightIdentity x = append x mempty == x

Operations
~~~~~~~~~~

.. code-block:: haskell

   data MonoidOperation a
     = MonoidSemigroup (SemigroupOperation a)
     | MonoidLeftIdentity
     | MonoidRightIdentity

   performMonoid :: Monoid a =>
     MonoidOperation a -> a -> Bool
   performMonoid op x = case op of
     MonoidSemigroup op' ->
       performSemigroup op' x
     MonoidLeftIdentity ->
       isLeftIdentity x
     MonoidRightIdentity ->
       isRightIdentity x

JSON
****

.. code-block:: haskell

   encodeJson :: MonoidOperation Json -> Json
   encodeJson op = case op of
     MonoidSemigroup op' -> {"semigroup": encodeJson op'}
     MonoidLeftIdentity -> "leftIdentity"
     MonoidRightIdentity -> "rightIdentity"

Binary
******

.. code-block:: haskell

   encodeBinary :: MonoidOperation ByteString -> ByteString
   encodeBinary op = case op of
     MonoidSemigroup op' ->
       (byteAsByteString 0) ++ op'
     MonoidLeftIdentity ->
       (byteAsByteString 1)
     MonoidRightIdentity ->
       (byteAsByteString 2)
