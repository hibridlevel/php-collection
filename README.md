PHP Collection
==============
This library adds basic collections for PHP. It's based on J. M. Schmitt work. The reason behind this fork is that original repository haven't been updated for quite some time aтd it laсks some musthave functionality.

Original code was refactored and now requires PHP 5.5, updated with several usefull methods like ::flatMap(), ::map(), ::foldLeft(), ::headOption(),  ::lastOption(), ::tail()  and e.t.c. Inspired by Scala collections. Added type-hinting.

Note
-------
Be adviced,that this documentation is not full and refers to forked repository of @schmittjoh. 

Collections can be seen as more specialized arrays for which certain contracts are guaranteed.

Supported Collections:

- Sequences

  - Keys: numerical, consequentially increasing, no gaps
  - Values: anything, duplicates allowed
  - Classes: ``Sequence``, ``SortedSequence``


- Maps

  - Keys: strings or objects, duplicate keys not allowed
  - Values: anything, duplicates allowed
  - Classes: ``Map``, ``LRUMap``


- Sets

  - Keys: not meaningful
  - Values: objects, or scalars, each value is guaranteed to be unique (see Set usage below for details)
  - Classes: ``Set``

General Characteristics:

- Collections are mutable (new elements may be added, existing elements may be modified or removed). Specialized
  immutable versions may be added in the future though.
- Equality comparison between elements are always performed using the shallow comparison operator (===).
- Sorting algorithms are unstable, that means the order for equal elements is undefined (the default, and only PHP behavior).


Installation
------------
PHP Collection can easily be installed via composer (in process)

.. code-block :: bash

    composer require imunhatep/collection

or add it to your ``composer.json`` file.

Usage
-----
Collection classes provide a rich API.

Sets
-----
In a Set each value is guaranteed to be unique. The ``Set`` class supports objects, and scalars as value. Equality
is determined via the following steps.

**Equality of Objects**

    1. If an object implements ``ObjectBasics``, equality is determined by the ``equals()`` method.
    2. If an object has an external handler like the ``DateTime`` that was registered via ``ObjectBasicsHandlerRegistry::registerHandlerFor``,
       equality is determined by that handler's ``equals()`` method.
    3. If none of the above is applicable, equality is determined by identity ``$a === $b``.

**Equality of Scalars**

    Scalar are considered equal if ``$a === $b`` is true.


.. code-block :: php

    $set = new Set();
    $set->add(new \DateTime('today'));
    $set->add(new \DateTime('today'));

    var_dump(count($set)); // int(1) -> the same date is not added twice

    foreach ($set as $date) {
        var_dump($date);
    }

    $set->all();
    $set->addSet($otherSet);
    $set->addAll($someElements);

    // Traverse
    $set->map(function($x) { return $x*2; });
    $set->flatMap(function($x) { return [$x*2, $x*4]; });


Sequences
---------

.. code-block :: php

    // Read Operations
    $seq = new Sequence([0, 2, 3, 2]);
    $seq->get(2); // int(3)
    $seq->all(); // [0, 2, 3, 2]

    $seq->head(); // Some(0)
    $seq->tail(); // Some(2)

    // Write Operations
    $seq = new Sequence([1, 5]);
    $seq->get(0); // int(1)
    $seq->update(0, 4);
    $seq->get(0); // int(4)
    $seq->remove(0);
    $seq->get(0); // int(5)

    $seq = new Sequence([1, 4]);
    $seq->add(2);
    $seq->all(); // [1, 4, 2]
    $seq->addAll(array(4, 5, 2));
    $seq->all(); // [1, 4, 2, 4, 5, 2]

    // Sort
    $seq = new Sequence([0, 5, 4, 2]);
    $seq->sortWith(function($a, $b) { return $a - $b; });
    $seq->all(); // [0, 2, 4, 5]

Maps
----

.. code-block :: php

    // Read Operations
    $map = new Map(['foo' => 'bar', 'baz' => 'boo']);
    $map->get('foo'); // Some('bar')
    $map->get('foo')->get(); // string('bar')
    $map->keys(); // ['foo', 'baz']
    $map->values(); // ['bar', 'boo']

    $map->headOption(); // Some(['key', 'value'])
    $map->head();       // null | ['key', 'value']
    $map->lastOption(); // Some(['key', 'value'])
    $map->last();       // null | ['key', 'value']
    
    $map->headOption()->get(); // ['foo', 'bar']
    $map->lastOption()->get(); // ['baz', 'boo']
    
    $map->tail();            // ['baz' => 'boo']

    iterator_to_array($map); // ['foo' => 'bar', 'baz' => 'boo']
    $map->all()              // ['foo' => 'bar', 'baz' => 'boo']

    // Write Operations
    $map = new Map();
    $map->set('foo', 'bar');
    $map->setAll(array('bar' => 'baz', 'baz' => 'boo'));
    $map->remove('foo');

    // Sort
    $map->sortWith('strcmp');

    // Transformation
    $map->map(function($key, $value) { return $value * 2; });
    $map->flatMap(function($key, $value) { return (new Map())->set($key, $value * 2); });
    
    $map->foldLeft(Object $s, function(SomeObject $s, $k, $v){ $s->add([$k, $v * 2]); return $s; })


License
-------

The code is released under the business-friendly `Apache2 license`_.

Documentation is subject to the `Attribution-NonCommercial-NoDerivs 3.0 Unported
license`_.

.. _Apache2 license: http://www.apache.org/licenses/LICENSE-2.0.html
.. _Attribution-NonCommercial-NoDerivs 3.0 Unported license: http://creativecommons.org/licenses/by-nc-nd/3.0/
