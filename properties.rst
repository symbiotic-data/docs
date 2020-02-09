.. _properties:

Algebraic Properties
====================

The various data types defined in Symbiotic-Data form various algebraic objects, and by extension support
certain properties over their operations.

The superclass structure is as follows:

.. code-block:: none

   Semigroup
     => Monoid

   Eq
     => Ord
       => Enum

   Semiring
     => Ring
       => DivisionRing ------\
       => CommutativeRing     => Field
         => EuclideanRing ---/

That is, a ``Field`` is both a ``DivisionRing`` and a ``EuclideanRing``, following the `PureScript taxonomy <https://pursuit.purescript.org/packages/purescript-prelude/4.1.0/docs/Data.Field>`_.
      
---------------


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

---------------

Eq
----

Eq has one operation defined on it, ``eq``, and it should be an `equivalence relation <https://en.wikipedia.org/wiki/Equivalence_relation>`_ (reflexive, symmetric, and transitive), and should support negation.

.. code-block:: haskell

   class Eq a where
     (==) :: a -> a -> Bool
     (/=) :: a -> a -> Bool
     x /= y = not (x == y) -- default instance

   not :: Bool -> Bool
   not True = False
   not False = True

   implies :: Bool -> Bool -> Bool
   implies True True = True
   implies True False = False
   implies False True = True
   implies False False = True
   -- alternative definition
   implies p q = if p then q else True

   isReflexive :: Eq a =>
     a -> Bool
   isReflexive x = x == x

   isSymmetric :: Eq a =>
     a -> a -> Bool
   isSymmetric x y = (x == y) `implies` (y == x)

   isTransitive :: Eq a =>
     a -> a -> a -> Bool
   isTransitive x y z = ((x == y) && (y == z)) `implies` (x == z)

   hasNegation :: Eq a =>
     a -> a -> Bool
   hasNegation x y = (x /= y) `implies` (not (x == y))

Operations
~~~~~~~~~~

.. code-block:: haskell

   data EqOperation a
     = EqReflexive
     | EqSymmetry a
     | EqTransitive a a
     | EqNegation a

   performEq :: Eq a =>
     EqOperation a -> a -> Bool
   performEq op x = case op of
     EqReflexive ->
       isReflexive x
     EqSymmetric y ->
       isSymmetry x y
     EqTransitive y z ->
       isTransitive x y z
     EqNegation y ->
       hasNegation x y

JSON
****

.. code-block:: haskell

   encodeJson :: EqOperation Json -> Json
   encodeJson op = case op of
     EqReflexive -> "reflexive"
     EqSymmetry y -> {"symmetry": y}
     EqTransitive y z -> {"transitive": {"y": y, "z": z}}
     EqNegation y -> {"negation": y}

Binary
******

.. code-block:: haskell

   encodeBinary :: EqOperation ByteString -> ByteString
   encodeBinary op = case op of
     EqReflexive ->
       (byteAsByteString 0)
     EqSymmetry y ->
       (byteAsByteString 1) ++ y
     EqTransitive y z ->
       (byteAsByteString 2) ++ y ++ z
     EqNegation y ->
       (byteAsByteString 3) ++ y

---------------

Ord
----

Ord is a superclass of Eq_ and inherit all of its faculties. It has one additional operation
defined on it, ``compare``, and it should facilitate a `partial order <https://en.wikipedia.org/wiki/Partially_ordered_set>`_
through the ``Ordering`` type.

.. code-block:: haskell

   data Ordering = LT | EQ | GT

   class Ord a where
     compare :: a -> a -> Ordering

   (<=) :: Ord a => a -> a -> Bool
   x <= y = case compare x y of
     LT -> True
     Eq -> True
     GT -> False

   isReflexive :: Ord a =>
     a -> Bool
   isReflexive x = x <= x

   isAntisymmetric :: Ord a =>
     a -> a -> Bool
   isAntisymmetric x y = ((x <= y) && (y <= x)) `implies` (x == y)

   isTransitive :: Ord a =>
     a -> a -> a -> Bool
   isTransitive x y z = ((x <= y) && (y <= z)) `implies` (x <= z)
