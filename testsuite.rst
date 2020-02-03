.. _testsuite:

Symbiote Test Suite
===================

The test suite is a simple idea --- there are two peers ``A`` and ``B`` (each on potentially different platforms),
with some peer-to-peer communications channel between them, which we'll call ``Socket``
(for instance, `ZeroMQ <https://zeromq.org>`_ or a WebSocket). ``Socket`` only understands some target data type,
which we'll also call ``Target`` (for instance, ``ByteString`` or ``ArrayBuffer`` for ZeroMQ,
and ``Json`` for WebSockets).

.. code-block:: none

    ________                   ________
   | Peer A |      Socket     | Peer B |
   |        |  -------------- |        |
   |________|     (Target)    |________|


Now, both ``Peer A`` and ``Peer B`` understand some "idea" of a data type, which we'll call ``Type T``. The
whole purpose of this test suite is to verify that both peers have an *identical* understanding of ``Type T``,
with respect to how `T` *operates*, **and** how it *translates* to-and-from ``Target``.

So, each peer has an idea of what ``T`` is, and some static set of operations that can be performed on ``T``.
The "operations" on ``T`` need to be extrapolated into a data type as well, such that every return type
when performed on a ``T`` is homogeneous. For instance, if I have functions ``f :: T -> T`` and ``g :: T -> Bool``,
then I need to come up with a data type that that can combine them. For instance:

.. code-block:: haskell

   data OperationsOnT
     = F
     | G
   
   perform :: OperationsOnT -> T -> Either T Bool
   perform op x = case op of
     F -> Left (f x)
     G -> Right (g x)


``Either T Bool`` is the "homogeneous return type" of *all* the supported operations on my type ``T``.

As we proceed, our idea of the network might look like this:

.. code-block:: none

    ________                   ________
   | Peer A |      Socket     | Peer B |
   |        |  -------------- |        |
   |________|     (Target)    |________|
   |    T   |                 |   T    |
    \______/                   \______/ 


The entire purpose of this protocol is to test that remote procedure calls are identical to local calls,
when the data and operation are the same --- given that the serialization method is identical, as well.

First and Second
----------------

Now for the actual protocol.

The protocol assumes its being run on a 1-to-1 communication mechanism --- there's no support for broadcasted
and subscribed messages; only *one* other peer will be tested while the other is running. This implies
a constraint on
how our protocol will operate --- one peer needs to be the "first" to operate, and the other needs to be the
"second".

It's not rocket science --- if we're generating data and expecting the other party to operate on that
data, then someone has to go first while the other listens for it. If they both went at the same time, there
would be a race condition. If they both were expecting data, then there'd be a deadlock. Therefore,
there should be some decision made by the programmer (you) on which peer will be the "first" and which will
be "second". It doesn't make a difference in the semantic integrity of the test suite --- they both will
generate and consume the same amount of random data.

For simplicity's sake, let's make ``Peer A`` the first, and ``Peer B`` the second.


.. code-block:: none

    ________                   ________
   |  First |                 | Second |
   |________|                 |________|
   | Peer A |      Socket     | Peer B |
   |        |  -------------- |        |
   |________|     (Target)    |________|
   |    T   |                 |   T    |
    \______/                   \______/ 


Generation
----------

Now, both peers will take turns in generating, consuming, and exchanging data. ``First`` will be (you guessed it)
the first one to take a step in that dance. Before they start, though, let's talk about data generation for
a quick second.

If you've ever heard of `QuickCheck <https://hackage.haskell.org/package/QuickCheck>`_ or property-based testing,
what is about to be said should come to no surprise for you.
For everyone else, though, it might seem a bit alien.

The act of generating random data implies that there's some **"size"** associated with how large that data
should be.

Think about it; if we just didn't care about the general size of that random data, and we were
generating an array, the computer could just generate an infinite set of data just as likely as an empty
array. What's going to
tell it to stop? A ``size`` parameter, that's what.

So, in our test suite, every data type we'll be verifying (through generation) **must** have some maximum
size (as an integer), which we'll approach starting from ``0``.

The first thing that our peers do is agree upon a set of types to test on, and their respective sizes -
each peer will take a turn generating the data, incrementing their own counters until they each hit
the maximum (around the same time).

The type's name is typically used in the message, but for our intents and
purposes, we'll call it a "topic", because it's just a string. If the programmer chooses to use a different
topic than the type's name, then there's no issue, so long as both peers expect the same topic/size pairings.
We'll call this message ``AvailableTopics``, and it's sent by ``First``.


.. code-block:: none

    ________                   ________
   |  First |                 | Second |
   |________|                 |________|
   | Peer A |      Socket     | Peer B |
   |        |  -------------- |        |
   |________|     (Target)    |________|
   |    T   |                 |   T    |
    \______/                   \______/ 
               ------------->
               AvailableTopics


Just to be clear, ``AvailableTopics`` is a mapping of *topics* (usually type names) to *sizes*.

If ``Peer B`` disagrees with the topics or sizes, it'll throw an error, and reflect that error back to ``Peer A``
so it can explode loudly as well. If all is well, then ``Peer B`` will tell ``Peer A`` to start, with the subset of
``Peer A``'s topics actually in use for this session:

.. code-block:: none

    ________                   ________
   |  First |                 | Second |
   |________|                 |________|
   | Peer A |      Socket     | Peer B |
   |        |  -------------- |        |
   |________|     (Target)    |________|
   |    T   |                 |   T    |
    \______/                   \______/ 
               ------------->
               AvailableTopics
               <-------------
                   Start



First Generating, Second Operating
----------------------------------

Now everything is ready to go! Let's get started. First thing is first, ``First`` will generate a random
value of type ``T`` (which we'll call ``x :: T``), and a random *operation* on type ``T`` as ``OperationsOnT``
(which we'll call ``op :: OperationsOnT``), with a size-index of ``0``.

Here, it will pack them both together in a message called ``Generated`` and send it over the wire to
``Second`` (serialized in the type ``Target``, remember).


.. code-block:: none

    ________                   ________
   |  First |                 | Second |
   |________|                 |________|
   | Peer A |      Socket     | Peer B |
   |        |  -------------- |        |
   |________|     (Target)    |________|
   |    T   |                 |   T    |
    \______/                   \______/ 
               ------------->
               AvailableTopics
               <-------------
                   Start
   
     (x,op)    ------------->
               First Generated


``Second`` will then decode the message and see a value and
an operation. All it has to do from here is perform the
operation ``op`` on ``x``, and get a result (which we'll call ``y``). It sends this result back to ``First``
as a message called ``Second Operated``.


.. code-block:: none

    ________                   ________
   |  First |                 | Second |
   |________|                 |________|
   | Peer A |      Socket     | Peer B |
   |        |  -------------- |        |
   |________|     (Target)    |________|
   |    T   |                 |   T    |
    \______/                   \______/ 
               ------------->
               AvailableTopics
               <-------------
                   Start
   
     (x,op)    ------------->
               First Generated
               <-------------  y=op(x)
               Second Operated



Next, ``First`` has to verify that ``y`` is indeed ``op(x)``. If it's not, explode loudly, and tell ``Second``
to do the same. If it's good, then tell ``Second`` it's their turn, with a message called ``YourTurn``.

.. code-block:: none

    ________                   ________
   |  First |                 | Second |
   |________|                 |________|
   | Peer A |      Socket     | Peer B |
   |        |  -------------- |        |
   |________|     (Target)    |________|
   |    T   |                 |   T    |
    \______/                   \______/ 
               ------------->
               AvailableTopics
               <-------------
                   Start
   
     (x,op)    ------------->
               First Generated
               <-------------  y=op(x)
    y=op(x)?   Second Operated
      true     ------------->
                  YourTurn


First Operating, Second Generating
----------------------------------

The tables have turned! ``First`` is now operating, and ``Second`` gets to generate. The entire process is
exactly the same, just reversed:


.. code-block:: none

    ________                   ________
   |  First |                 | Second |
   |________|                 |________|
   | Peer A |      Socket     | Peer B |
   |        |  -------------- |        |
   |________|     (Target)    |________|
   |    T   |                 |   T    |
    \______/                   \______/ 
               ------------->
               AvailableTopics
               <-------------
                   Start
   
     (x,op)    ------------->
               First Generated
               <-------------  y=op(x)
    y=op(x)?   Second Operated
      true     ------------->
                  YourTurn
   
               <-------------  (x,op)
              Second Generated
     y=op(x)   -------------> 
               First Operated y=op(x)?
               <-------------   true
                  YourTurn


Perfect. Each peer has now both generated and operated once, and has increased their size counter by `1`.
They will keep doing this until one of them reaches the max generation size. Can you guess which one
will be... first... in doing so?

I'm Finished
------------

I guess I gave it away --- ``First`` generates first, so it must also be the first to be finished
generating. It signals this by sending ``ImFinished`` instead of ``YourTurn``.


.. code-block:: none

    ________                   ________
   |  First |                 | Second |
   |________|                 |________|
   | Peer A |      Socket     | Peer B |
   |        |  -------------- |        |
   |________|     (Target)    |________|
   |    T   |                 |   T    |
    \______/                   \______/ 
               ------------->
               AvailableTopics
               <-------------
                   Start
   
     (x,op)    ------------->
               First Generated
               <-------------  y=op(x)
    y=op(x)?   Second Operated
      true     ------------->
                  YourTurn
   
               <-------------  (x,op)
              Second Generated
     y=op(x)   -------------> 
               First Operated y=op(x)?
               <-------------   true
                  YourTurn
   
                    ....
   
     (x,op)    ------------->
               First Generated
               <-------------  y=op(x)
    y=op(x)?   Second Operated
      true     ------------->
                 ImFinished


After ``Second`` receives that ``ImFinished`` message, it will send ``ImFinished`` also once
it has verified its last run.

.. code-block:: none

    ________                   ________
   |  First |                 | Second |
   |________|                 |________|
   | Peer A |      Socket     | Peer B |
   |        |  -------------- |        |
   |________|     (Target)    |________|
   |    T   |                 |   T    |
    \______/                   \______/ 
               ------------->
               AvailableTopics
               <-------------
                   Start
   
     (x,op)    ------------->
               First Generated
               <-------------  y=op(x)
    y=op(x)?   Second Operated
      true     ------------->
                  YourTurn
   
               <-------------  (x,op)
              Second Generated
     y=op(x)   -------------> 
               First Operated y=op(x)?
               <-------------   true
                  YourTurn
   
                    ....
   
     (x,op)    ------------->
               First Generated
               <-------------  y=op(x)
    y=op(x)?   Second Operated
      true     ------------->
                 ImFinished
   
               <-------------  (x,op)
              Second Generated
     y=op(x)   -------------> 
               First Operated y=op(x)?
               <-------------   true
                 ImFinished



The topic's entire routine has been completed and verified. By using random generation, we
can verify the *properties* of our data, instead of cherry-picked unit tests. At the same time, it will
verify both implementations for encoding and decoding our data are also correct. It is a powerful tool that
can help us catch edge
cases we didn't consider before hand, or reveal fundamental misunderstandings about how our platform works
under-the-hood.

Because our scenario only uses one topic ``T``, both peers will know that they are finished, and exit without
failure.



.. code-block:: none

    ________                   ________
   |  First |                 | Second |
   |________|                 |________|
   | Peer A |      Socket     | Peer B |
   |        |  -------------- |        |
   |________|     (Target)    |________|
   |    T   |                 |   T    |
    \______/                   \______/ 
               ------------->
               AvailableTopics
               <-------------
                   Start
   
     (x,op)    ------------->
               First Generated
               <-------------  y=op(x)
    y=op(x)?   Second Operated
      true     ------------->
                  YourTurn
   
               <-------------  (x,op)
              Second Generated
     y=op(x)   -------------> 
               First Operated y=op(x)?
               <-------------   true
                  YourTurn
   
                    ....
   
     (x,op)    ------------->
               First Generated
               <-------------  y=op(x)
    y=op(x)?   Second Operated
      true     ------------->
                 ImFinished
   
               <-------------  (x,op)
              Second Generated
     y=op(x)   -------------> 
               First Operated y=op(x)?
               <-------------   true
                 ImFinished
   
    ________                   ________
   |  Done  |                 |  Done  |
   |________|                 |________|

