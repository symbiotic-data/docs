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
     MonoidSemigroup op' ->
       {"semigroup": encodeJson op'}
     MonoidLeftIdentity ->
       "leftIdentity"
     MonoidRightIdentity ->
       "rightIdentity"

Binary
******

.. code-block:: haskell

   encodeBinary :: MonoidOperation ByteString -> ByteString
   encodeBinary op = case op of
     MonoidSemigroup op' ->
       (byteAsByteString 0) ++ encodeBinary op'
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
     EqReflexive ->
       "reflexive"
     EqSymmetry y ->
       {"symmetry": y}
     EqTransitive y z ->
       {"transitive": {"y": y, "z": z}}
     EqNegation y ->
       {"negation": y}

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

Operations
~~~~~~~~~~

.. code-block:: haskell

   data OrdOperation a
     = OrdEq (EqOperation a)
     | OrdReflexive
     | OrdAntiSymmetry a
     | OrdTransitive a a

   performOrd :: Ord a =>
     OrdOperation a -> a -> Bool
   performOrd op x = case op of
     OrdEq op' ->
       performEq op' x
     OrdReflexive ->
       isReflexive x
     OrdAntiSymmetry y ->
       isAntiSymmetric x y
     OrdTransitive y z ->
       isTransitive x y z

JSON
****

.. code-block:: haskell

   encodeJson :: OrdOperation Json -> Json
   encodeJson op = case op of
     OrdEq op' ->
       {"eq": encodeJson op'}
     OrdReflexive ->
       "reflexive"
     OrdAntiSymmetry y ->
       {"antisymmetry": y}
     OrdTransitive y z ->
       {"transitive": {"y": y, "z": z}}

Binary
******

.. code-block:: haskell

   encodeBinary :: OrdOperation ByteString -> ByteString
   encodeBinary op = case op of
     OrdEq op' ->
       (byteAsByteString 0) ++ encodeBinary op'
     OrdReflexive ->
       (byteAsByteString 1)
     OrdAntiSymmetry y ->
       (byteAsByteString 2) ++ y
     OrdTransitive y z ->
       (byteAsByteString 3) ++ y ++ z

---------------

Enum
----

Enum is **not** a superclass of Ord_, but it uses its faculties in testing. It has four operations
defined on it, ``pred``, ``succ``, ``toEnum``, and ``fromEnum``.
``pred`` and ``succ`` should be opposite - `isomorphic <https://en.wikipedia.org/wiki/Isomorphism>`_ over composition.
Furthermore, ``fromEnum`` should be `homomorphic <https://en.wikipedia.org/wiki/Homomorphism>`_ over ``compare``.
Enums are `total orders <https://en.wikipedia.org/wiki/Total_order>`_.

.. code-block:: haskell

   class Enum a where
     pred :: a -> a
     succ :: a -> a
     toEnum :: Int -> Maybe a
     fromEnum :: a -> Int

   predsucc :: Enum a =>
     a -> Bool
   predsucc x = (pred (succ x)) == x

   succpred :: Enum a =>
     a -> Bool
   succpred x = (succ (pred x)) == x

   compareHom :: Enum a => Ord a =>
     a -> a -> Bool
   compareHom x y = (compare x y) == (compare (fromEnum x) (fromEnum y))

Operations
~~~~~~~~~~

.. code-block:: haskell

   data EnumOperation a
     = EnumOrd (OrdOperation a)
     | EnumCompareHom a
     | EnumPredSucc
     | EnumSuccPred

   performEnum :: Enum a => Ord a =>
     EnumOperation a -> a -> Bool
   performEnum op x = case op of
     EnumOrd op' ->
       perfromOrd op' x
     EnumCompareHom y ->
       compareHom x y
     EnumPredSucc ->
       predsucc x
     EnumSuccPred ->
       succpred x

JSON
****

.. code-block:: haskell

   encodeJson :: EnumOperation Json -> Json
   encodeJson op = case op of
     EnumOrd op' ->
       {"ord": enocdeJson op'}
     EnumCompareHom y ->
       {"compareHom": y}
     EnumPredSucc ->
       "predsucc"
     EnumSuccPred ->
       "succpred"

Binary
******

.. code-block:: haskell

   encodeBinary :: EnumOperation ByteString -> ByteString
   encodeBinary op = case op of
     EnumOrd op' ->
       (byteAsByteString 0) ++ encodeBinary op'
     EnumCompareHom y ->
       (byteAsByteString 1) ++ y
     EnumPredSucc ->
       (byteAsByteString 2)
     EnumSuccPred ->
       (byteAsByteString 3)

---------------

Semiring
--------

Semiring has four operations defined on it, ``zero``, ``one``, ``add``, and ``mul``. It should form a `semiring <https://en.wikipedia.org/wiki/Semiring>`_.

.. code-block:: haskell

   class Semiring a where
     zero :: a
     one :: a
     add :: a -> a -> a
     mul :: a -> a -> a

   associative :: (a -> a -> a) -> a -> a -> a -> Bool
   associative f x y z = (f x (f y z)) == (f (f x y) z)

   commutative :: (a -> a -> a) -> a -> a -> Bool
   commutative f x y = (f x y) == (f y x)

   distributive :: (a -> a) -> (a -> a -> a) -> a -> a -> Bool
   distributive f g x y = (f (g x y)) == (g (f x) (f y))

   isCommutativeMonoid :: Semiring a =>
     a -> a -> a -> Bool
   isCommutativeMonoid x y z =
     (associative add x y z)
       && (commutative add x y)
       -- zero is the empty element for add
       && ((add x zero) == x)

   isMonoid :: Semiring a =>
     a -> a -> a -> Bool
   isMonoid x y z =
     (associative mul x y z)
       -- one is the empty element for mul
       && ((mul x one) == x)

   isLeftDistributive :: Semiring a =>
     a -> a -> a -> Bool
   isLeftDistributive x y z =
     distributive (\q -> mul x q) add y z

   isRightDistributive :: Semiring a =>
     a -> a -> a -> Bool
   isRightDistributive x y z =
     distributive (\q -> mul q x) add y z

   hasAnnihilation :: Semiring a =>
     a -> Bool
   hasAnnihilation x =
     ((mul x 0) == (mul 0 x))
       && ((mul x 0) == 0)

Operations
~~~~~~~~~~

.. code-block:: haskell

   data SemiringOperation a
     = SemiringCommutativeMonoid a a
     | SemiringMonoid a a
     | SemiringLeftDistributive a a
     | SemiringRightDistributive a a
     | SemiringAnnihilation

   performSemiring :: Semiring a =>
     SemiringOperation a -> a -> Bool
   performSemiring op x = case op of
     SemiringCommutativeMonoid y x ->
       isCommutativeMonoid x y z
     SemiringMonoid y z ->
       isMonoid x y z
     SemiringLeftDistributive y z ->
       isLeftDistributive x y z
     SemiringRightDistributive y z ->
       isRightDistributive x y z
     SemiringAnnihilation ->
       hasAnnihilation x

JSON
****

.. code-block:: haskell

   encodeJson :: SemiringOperation Json -> Json
   encodeJson op = case op of
     SemiringCommutativeMonoid y z ->
       {"commutativeMonoid": {"y": y, "z": z}}
     SemiringMonoid y z ->
       {"monoid": {"y": y, "z": z}}
     SemiringLeftDistributive y z ->
       {"leftDistributive": {"y": y, "z": z}}
     SemiringRightDistributive y z ->
       {"rightDistributive": {"y": y, "z": z}}
     SemiringAnnihilation ->
       "annihilation"

Binary
******

.. code-block:: haskell

   encodeBinary :: SemiringOperation ByteString -> ByteString
   encodeBinary op = case op of
     SemiringCommutativeMonoid y z ->
       (byteToByteString 0) ++ y ++ z
     SemiringMonoid y z ->
       (byteToByteString 1) ++ y ++ z
     SemiringLeftDistributive y z ->
       (byteToByteString 2) ++ y ++ z
     SemiringRightDistributive y z ->
       (byteToByteString 3) ++ y ++ z
     SemiringAnnihilation ->
       (byteToByteString 4)
      