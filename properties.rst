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
       => Enum ------\
                      => BoundedEnum
       => Bounded ---/

   HeytingAlgebra
     => BooleanAlgebra

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

   class Eq a => Ord a where
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

Enum is **not** a superclass of Ord_, but it uses its faculties in testing. It has two operations
defined on it, ``pred``, ``succ``.
``pred`` and ``succ`` should be opposite --- `isomorphic <https://en.wikipedia.org/wiki/Isomorphism>`_ over composition.

.. code-block:: haskell

   class Enum a where
     pred :: a -> a
     succ :: a -> a

   predsucc :: Enum a =>
     a -> Bool
   predsucc x = (pred (succ x)) == x

   succpred :: Enum a =>
     a -> Bool
   succpred x = (succ (pred x)) == x

Operations
~~~~~~~~~~

.. code-block:: haskell

   data EnumOperation a
     = EnumOrd (OrdOperation a)
     | EnumPredSucc
     | EnumSuccPred

   performEnum :: Enum a => Ord a =>
     EnumOperation a -> a -> Bool
   performEnum op x = case op of
     EnumOrd op' ->
       perfromOrd op' x
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
     EnumPredSucc ->
       (byteAsByteString 1)
     EnumSuccPred ->
       (byteAsByteString 2)

---------------

Bounded
-------

Bounded is **not** a superclass of Ord_, but it uses its faculties in testing. It has two values
defined on it, ``top`` and ``bottom``.
Types that are Bounded are `bounded lattices <https://en.wikipedia.org/wiki/Lattice_(order)#Bounded_lattice>`_.

.. code-block:: haskell

   class Bounded a where
     top :: a
     bottom :: a

   isBetween :: Bounded a => Ord a =>
     a -> Bool
   isBetween x = (x <= top) && (bottom <= x)

Operations
~~~~~~~~~~

.. code-block:: haskell

   data BoundedOperation a
     = BoundedOrd (OrdOperation a)
     | BoundedBetween

   performBounded :: Bounded a => Ord a =>
     BoundedOperation a -> a -> Bool
   performBounded op x = case op of
     BoundedOrd op' ->
       performOrd op' x
     BoundedBetween ->
       isBetween x

JSON
****

.. code-block:: haskell

   encodeJson :: BoundedOperation Json -> Json
   encodeJson op = case op of
     BoundedOrd op' ->
       {"ord": encodeJson op'}
     BoundedBetween ->
       "between"

Binary
******

.. code-block:: haskell

   encodeBinary :: BoundedOperation ByteString -> ByteString
   encodeBinary op = case op of
     BoundedOrd op' ->
       (byteToByteString 0) ++ encodeBinary op'
     BoundedBetween ->
       (byteToByteString 1)

---------------

BoundedEnum
-----------

BoundedEnum is a superclass of both Enum_ and Bounded_, and inherit all of their faculties. It has two additional operations
defined on it, ``toEnum`` and ``fromEnum``. They should be opposite functions to each other, and
furthermore, ``fromEnum`` should be `homomorphic <https://en.wikipedia.org/wiki/Homomorphism>`_ over ``compare``.
BoundedEnums are `total orders <https://en.wikipedia.org/wiki/Total_order>`_.

.. code-block:: haskell

   class (Bounded a, Enum a) => BoundedEnum a where
     toEnum :: Int -> Maybe a
     fromEnum :: a -> Int

   compareHom :: BoundedEnum a => Ord a =>
     a -> a -> Bool
   compareHom x y = (compare x y) == (compare (fromEnum x) (fromEnum y))

   fromPredMapping :: BoundedEnum a =>
     a -> Bool
   fromPredMapping x = (fromEnum (pred x)) == (pred (fromEnum x))

   fromSuccMapping :: BoundedEnum a =>
     a -> Bool
   fromSuccMapping x = (fromEnum (succ x)) == (succ (fromEnum x))

   toFromIsomorphism :: Enum a =>
     a -> Bool
   toFromIsomorphism x = (toEnum (fromEnum x)) == (Just x)

Operations
~~~~~~~~~~

.. code-block:: haskell

   data BoundedEnumOperation a
     = BoundedEnumEnum (EnumOperation a)
     | BoundedEnumBounded (BoundedOperation a)
     | BoundedEnumCompareHom a
     | BoundedEnumFromPred
     | BoundedEnumFromSucc
     | BoundedEnumToFromIso

   performBoundedEnum :: BoundedEnum a => Ord a =>
     BoundedEnumOperation a -> a -> Bool
   performBoundedEnum op x = case op of
     BoundedEnumEnum op' ->
       performEnum op' x
     BoundedEnumBounded op' ->
       performBounded op' x
     BoundedEnumCompareHom y ->
       compareHom x y
     BoundedEnumFromPred ->
       fromPredMapping x
     BoundedEnumFromSucc ->
       fromSuccMapping x
     BoundedEnumToFromIso ->
       toFromIsomorphism x

JSON
****

.. code-block:: haskell

   encodeJson :: BoundedEnumOperation Json -> Json
   encodeJson op = case op of
     BoundedEnumEnum op' ->
       {"enum": encodeJson op'}
     BoundedEnumBounded op' ->
       {"bounded": encodeJson op'}
     BoundedEnumCompareHom y ->
       {"compareHom": y}
     BoundedEnumFromPred ->
       "fromPred"
     BoundedEnumFromSucc ->
       "fromSucc"
     BoundedEnumToFromIso ->
       "toFromIso"

Binary
******

.. code-block:: haskell

   encodeBinary :: BoundedEnumOperation ByteString -> ByteString
   encodeBinary op = case op of
     BoundedEnumEnum op' ->
       (byteAsByteString 0) ++ encodeBinary op'
     BoundedEnumBounded op' ->
       (byteAsByteString 1) ++ encodeBinary op'
     BoundedEnumCompareHom y ->
       (byteAsByteString 2) ++ y
     BoundedEnumFromPred ->
       (byteAsByteString 3)
     BoundedEnumFromSucc ->
       (byteAsByteString 4)
     BoundedEnumToFromIso ->
       (byteAsByteString 5)

---------------

HeytingAlgebra
--------------

HeytingAlgebra has six operations defined on it, ``ff``, ``tt``, ``implies``, ``conj``, ``disj``, and ``not``.
It should form a `heyting algebra <https://en.wikipedia.org/wiki/Heyting_algebra>`_.

.. code-block:: haskell

   class HeytingAlgebra a where
     ff :: a
     tt :: a
     implies :: a -> a -> a
     conj :: a -> a -> a
     disj :: a -> a -> a
     not :: a -> a

   isDisjAssociative :: HeytingAlgebra a =>
     a -> a -> a -> Bool
   isDisjAssociative x y z = (disj x (disj y z)) == (disj (disj x y) z)

   isConjAssociative :: HeytingAlgebra a =>
     a -> a -> a -> Bool
   isConjAssociative x y z = (conj x (conj y z)) == (conj (conj x y) z)

   isDisjCommutative :: HeytingAlgebra a =>
     a -> a -> Bool
   isDisjCommutative x y = (disj x y) == (disj y x)

   isConjCommutative :: HeytingAlgebra a =>
     a -> a -> Bool
   isConjCommutative x y = (conj x y) == (conj y x)

   disjConjAbsorption :: HeytingAlgebra a =>
     a -> a -> Bool
   disjConjAbsorption x y = (disj x (conj x y)) == x

   conjDisjAbsorption :: HeytingAlgebra a =>
     a -> a -> Bool
   conjDisjAbsorption x y = (conj x (disj x y)) == x

   isDisjIdempotent :: HeytingAlgebra a =>
     a -> Bool
   isDisjIdempotent x = (disj x x) == x

   isConjIdempotent :: HeytingAlgebra a =>
     a -> Bool
   isConjIdempotent x = (conj x x) == x

   disjIdentity :: HeytingAlgebra a =>
     a -> Bool
   disjIdentity x = (disj x ff) == x

   conjIdentity :: HeytingAlgebra a =>
     a -> Bool
   conjIdentity x = (conj x tt) == x

   implicationTop :: HeytingAlgebra a =>
     a -> Bool
   implicationTop x = (implies x x) == tt

   implicationApplication :: HeytingAlgebra a =>
     a -> a -> Bool
   implicationApplication x y = (conj x (implies x y)) == (conj x y)

   implicationConclusion :: HeytingAlgebra a =>
     a -> a -> Bool
   implicaitonConclusion x y = (conj y (implies x y)) == y

   implicationDistributive :: HeytingAlgebra a =>
     a -> a -> a -> Bool
   implicationDistributive x y z = (implies x (conj y z)) == (conj (implies x y) (implies x z))

   hasCompliment :: HeytingAlgebra a =>
     a -> Bool
   hasCompliment x = (not a) == (implies x ff)

Operations
~~~~~~~~~~

.. code-block:: haskell

   data HeytingAlgebraOperation a
     = DisjAssociative a a
     | ConjAssociative a a
     | DisjCommutative a
     | ConjCommutative a
     | DisjConjAbsorption a
     | ConjDisjAbsorption a
     | DisjIdempotent
     | ConjIdempotent
     | DisjIdentity
     | ConjIdentity
     | ImplicationTop
     | ImplicationApplication a
     | ImplicationConclusion a
     | ImplicationDistribution a a
     | Compliment

   performHeytingAlgebra :: HeytingAlgebra a =>
     HeytingAlgebraOperation a -> a -> Bool
   performHeytingAlgebra op x = case op of
     DisjAssociative y z ->
       isDisjAssociatve x y z
     ConjAssociative y z ->
       isConjAssociatve x y z
     DisjCommutative y ->
       isDisjCommutative x y
     ConjCommutative y ->
       isConjCommutative x y
     DisjConjAbsorption y ->
       disjConjAbsorption x y
     ConjDisjAbsorption y ->
       conjDisjAbsorption x y
     DisjIdempotent ->
       isDisjIdempotent x
     ConjIdempotent ->
       isConjIdempotent x
     DisjIdentity ->
       disjIdentity x
     ConjIdentity ->
       conjIdentity x
     ImplicationTop ->
       implicationTop x
     ImplicationApplication y ->
       implicationApplication x y
     ImplicationConclusion y ->
       implicationConclusion x y
     ImplicationDistributive y z ->
       implicationDistributive x y z
     Compliment ->
       hasCompliment x

JSON
****

.. code-block:: haskell

   encodeJson :: HeytingAlgebraOperation Json -> Json
   encodeJson op = case op of
     DisjAssociative y z ->
       {"disjAssociative": {"y": y, "z": z}}
     ConjAssociative y z ->
       {"conjAssociative": {"y": y, "z": z}}
     DisjCommutative y ->
       {"disjCommutative": y}
     ConjCommutative y ->
       {"conjCommutative": y}
     DisjConjAbsorption y ->
       {"disjConjAbsorption": y}
     ConjDisjAbsorption y ->
       {"conjDisjAbsorption": y}
     DisjIdempotent ->
       "disjIdempotent"
     ConjIdempotent ->
       "conjIdempotent"
     DisjIdentity ->
       "disjIdentity"
     ConjIdentity ->
       "conjIdentity"
     ImplicationTop ->
       "implicationTop"
     ImplicationApplication y ->
       {"implicationApplication": y}
     ImplicationConclusion y ->
       {"implicationConclusion": y}
     ImplicationDistributive y z ->
       {"implicationDistributive": {"y": y, "z": z}}
     Compliment ->
       "compliment"

Binary
******

.. code-block:: haskell

   encodeBinary :: HeytingAlgebraOperation ByteString -> ByteString
   encodeBinary op = case op of
     DisjAssociative y z ->
       (byteToByteString 0) ++ y ++ z
     ConjAssociative y z ->
       (byteToByteString 1) ++ y ++ z
     DisjCommutative y ->
       (byteToByteString 2) ++ y
     ConjCommutative y ->
       (byteToByteString 3) ++ y
     DisjConjAbsorption y ->
       (byteToByteString 4) ++ y
     ConjDisjAbsorption y ->
       (byteToByteString 5) ++ y
     DisjIdempotent ->
       (byteToByteString 6)
     ConjIdempotent ->
       (byteToByteString 7)
     DisjIdentity ->
       (byteToByteString 8)
     ConjIdentity ->
       (byteToByteString 9)
     ImplicationTop ->
       (byteToByteString 10)
     ImplicationApplication y ->
       (byteToByteString 11) ++ y
     ImplicationConclusion y ->
       (byteToByteString 12) ++ y
     ImplicationDistributive y z ->
       (byteToByteString 13) ++ y ++ z
     Compliment ->
       (byteToByteString 14)

---------------
       
BooleanAlgebra
--------------

BooleanAlgebra is a superclass of HeytingAlgebra_ and inherit all of its faculties. It has no additional
operations defined on it, but it should facilitate a `boolean algebra <https://en.wikipedia.org/wiki/Boolean_algebra>`_, by supporting the `law of the excluded middle <https://en.wikipedia.org/wiki/Law_of_excluded_middle>`_.

.. code-block:: haskell

   class HeytingAlgebra a => BooleanAlgebra a

   hasLawOfExcludedMiddle :: BooleanAlgebra a =>
     a -> Bool
   hasLawOfExcludedMiddle x = (disj x (not x)) == tt

Operations
~~~~~~~~~~

.. code-block:: haskell

   data BooleanAlgebraOperation a
     = BooleanAlgebraHeytingAlgebra (HeytingAlgebraOperation a)
     | LawOfExcludedMiddle

   performBooleanAlgebra :: BooleanAlgebra a =>
     BooleanAlgebraOperation a -> a -> Bool
   performBooleanAlgebra op x = case op of
     BooleanAlgebraHeytingAlgebra op' ->
       performHeytingAlgebra op' x
     LawOfExcludedMiddle ->
       hasLawOfExcludedMiddle x

JSON
****

.. code-block:: haskell

   encodeJson :: BooleanAlgebraOperation Json -> Json
   encodeJson op = case op of
     BooleanAlgebraHeytingAlgebra op' ->
       {"heytingAlgebra": encodeJson op'}
     LawOfExcludedMiddle ->
       "lawOfExcludedMiddle"

Binary
******

.. code-block:: haskell

   encodeBinary :: BooleanAlgebraOperation ByteString -> ByteString
   encodeBinary op = case op of
     BooleanAlgebraHeytingAlgebra op' ->
       (byteToByteString 0) ++ encodeBinary op'
     LawOfExcludedMiddle ->
       (byteToByteString 1)


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

---------------

Ring
----

Ring is a superclass of Semiring_ and inherit all of its faculties. It has one additional operation
defined on it, ``sub``, and it should facilitate an `additive inverse <https://en.wikipedia.org/wiki/Additive_inverse>`_.

.. code-block:: haskell

   class Semiring a => Ring a where
     sub :: a -> a -> a

   isAdditiveInverse :: Ring a =>
     a -> Bool
   isAdditiveInverse x = (sub x x) == zero

Operations
~~~~~~~~~~

.. code-block:: haskell

   data RingOperation a
     = RingSemiring (SemiringOperation a)
     | RingAdditiveInverse

   performRing :: Ring a =>
     RingOperation a -> a -> Bool
   performRing op x = case op of
     RingSemiring op' ->
       performSemiring op' x
     RingAdditiveInverse ->
       isAdditiveInverse x

JSON
****

.. code-block:: haskell

   encodeJson :: RingOperation Json -> Json
   encodeJson op = case op of
     RingSemiring op' ->
       {"semiring": encodeJson op'}
     RingAdditiveInverse ->
       "additiveInverse"

Binary
******

.. code-block:: haskell

   encodeBinary :: RingOperation ByteString -> ByteString
   encodeBinary op = case op of
     RingSemiring op' ->
       (byteAsByteString 0) ++ encodeBinary op'
     RingAdditiveInverse ->
       (byteAsByteString 1)

---------------

DivisionRing
------------

DivisionRing is a superclass of Ring_ and inherit all of its faculties. It has one additional operation
defined on it, ``recip``, and it should facilitate a `multiplicative inverse <https://en.wikipedia.org/wiki/Multiplicative_inverse>`_.

.. code-block:: haskell

   class Ring a => DivisionRing a where
     recip :: a -> a

   isInverse :: DivisionRing a =>
     a -> Bool
   isInverse x = (x /= zero) `implies` ((mul x (recip x)) == one)

Operations
~~~~~~~~~~

.. code-block:: haskell

   data DivisionRingOperation a
     = DivisionRingRing (RingOperation a)
     | DivisionRingInverse

   performDivisionRing :: DivisionRing a =>
     DivisionRingOperation a -> a -> Bool
   performDivisionRing op x = case op of
     DivisionRingRing op' ->
       performRing op' x
     DivisionRingInverse ->
       isInverse x

JSON
****

.. code-block:: haskell

   encodeJson :: DivisionRingOperation Json -> Json
   encodeJson op = case op of
     DivisionRingRing op' ->
       {"ring": encodeJson op'}
     DivisionRingInverse ->
       "inverse"

Binary
******

.. code-block:: haskell

   encodeBinary :: DivisionRingOperation ByteString -> ByteString
   encodeBinary op = case op of
     DivisionRingRing op' ->
       (byteToByteString 0) ++ encodeBinary op'
     DivisionRingInverse ->
       (byteToByteString 1)

---------------

CommutativeRing
---------------

CommutativeRing is a superclass of Ring_ and inherit all of its faculties. It has no additional operation
defined on it, but assumes ``mul`` is `commutative <https://en.wikipedia.org/wiki/Commutative_property>`_.

.. code-block:: haskell

   class Ring a => CommutativeRing a

   isCommutative :: CommutativeRing a =>
     a -> a -> Bool
   isCommutative x y = (mul x y) == (mul y x)

Operations
~~~~~~~~~~

.. code-block:: haskell

   data CommutativeRingOperation a
     = CommutativeRingRing (RingOperation a)
     | CommutativeRingCommutative a

   performCommutativeRing :: CommutativeRing a =>
     CommutativeRingOperation a -> a -> Bool
   performCommutativeRing op x = case op of
     CommutativeRingRing op' ->
       performRing op' x
     CommutativeRingCommutative y ->
       isCommutative x y

JSON
****

.. code-block:: haskell

   encodeJson :: CommutativeRingOperation Json -> Json
   encodeJson op = case op of
     CommutativeRingRing op' ->
       {"ring": encodeJson op'}
     CommutativeRingCommutative y ->
       {"commutative": y}

Binary
******

.. code-block:: haskell

   encodeBinary :: CommutativeRingOperation ByteString -> ByteString
   encodeBinary op = case op of
     CommutativeRingRing op' ->
       (byteToByteString 0) ++ encodeBinary op'
     CommutativeRingCommutative y ->
       (byteToByteString 1) ++ y

---------------

EuclideanRing
-------------

EuclideanRing is a superclass of CommutativeRing_ and inherit all of its faculties. It has three additional operations
defined on it, ``mod``, ``div``, and ``degree``. It should facilitate a `Euclidean domain <https://en.wikipedia.org/wiki/Euclidean_domain>`_,
however, we can only test for the integral domain (due to language compatibility).

.. code-block:: haskell

   class CommutativeRing a => EuclideanRing a where
     degree :: a -> Int
     mod :: a -> a -> a
     div :: a -> a -> a

   isIntegralDomain :: EuclideanRing a =>
     a -> a -> Bool
   isIntegralDomain x y = ((x /= zero) && (y /= zero)) `implies` ((mul x y) /= zero)

Operations
~~~~~~~~~~

.. code-block:: haskell

   data EuclideanRingOperation a
     = EuclideanRingCommutativeRing (CommutativeRingOperation a)
     | EuclideanRingIntegralDomain a

   performEuclideanRing :: EuclideanRing a =>
     EuclideanRingOperation a -> a -> Bool
   performEuclideanRing op x = case op of
     EuclideanRingCommutativeRing op' ->
       performCommutativeRing op' x
     EuclideanRingIntegralDomain y ->
       isIntegralDomain x y

JSON
****

.. code-block:: haskell

   encodeJson :: EuclideanRingOperation Json -> Json
   encodeJson op = case op of
     EuclideanRingCommutativeRing op' ->
       {"commutativeRing": encodeJson op'}
     EuclideanRingIntegralDomain y ->
       {"integralDomain": y}

Binary
******

.. code-block:: haskell

   encodeBinary :: EuclideanRingOperation ByteString -> ByteString
   encodeBinary op = case op of
     EuclideanRingCommutativeRing op' ->
       (byteToByteString 0) ++ encodeBinary op'
     EuclideanRingIntegralDomain y ->
       (byteToByteString 1) ++ y

---------------

Field
-----

Field is a superclass of both DivisionRing_ and EuclideanRing_, and inherit all of their faculties. It has no additional operation
defined on it, but is a `field <https://en.wikipedia.org/wiki/Field_(mathematics)>`_.

.. code-block:: haskell

   class (DivisionRing a, EuclideanRing a) => Field a

Operations
~~~~~~~~~~

.. code-block:: haskell

   data FieldOperation a
     = FieldDivisionRing (DivisionRingOperation a)
     | FieldEuclideanRing (EuclideanRingOperation a)

   performField :: Field a =>
     FieldOperation a -> a -> Bool
   performField op x = case op of
     FieldDivisionRing op' ->
       performDivisionRing op' x
     FieldEuclideanRing op' ->
       performEuclideanRing op' x

JSON
****

.. code-block:: haskell

   encodeJson :: FieldOperation Json -> Json
   encodeJson op = case op of
     FieldDivisionRing op' ->
       {"divisionRing": encodeJson op'}
     FieldEuclideanRing op' ->
       {"euclideanRing": encodeJson op'}

Binary
******

.. code-block:: haskell

   encodeBinary :: FieldOperation ByteString -> ByteString
   encodeBinary op = case op of
     FieldDivisionRing op' ->
       (byteToByteString 0) ++ encodeBinary op'
     FieldEuclideanRing op' ->
       (byteToByteString 1) ++ encodeBinary op'
