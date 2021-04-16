aetcd3 for Project Aquarium
============================

.. image:: https://img.shields.io/badge/docs-aetcd3.rtfd.io-green.svg
   :alt: Documentation
   :target: https://aetcd3.readthedocs.io

.. image:: https://img.shields.io/pypi/pyversions/aetcd3.svg
   :alt: Supported Python Versions
   :target: https://pypi.python.org/pypi/aetcd3

.. image:: https://img.shields.io/github/license/martyanov/aetcd3
   :alt: License
   :target: https://github.com/martyanov/aetcd3/blob/master/LICENSE

Note Before
~~~~~~~~~~~~

This is a forked repository from `aetcd3`_ by @martyanov.

At Aquarist Labs we found it necessary to fork and maintain some patches we
need for the development of `Aquarium`_, simply because our development pace
may at times overtake the time it would take to merge patches into the original
repository.

We deeply believe in cooperating with the upstreams of the projects we use, and
thus we intend to contribute our efforts to aetcd3 as much as possible, should
they be welcome.

At this point in time, we have no plans to provide a PyPI package based on our
fork.


.. _aetcd3: https://github.com/martyanov/aetcd3
.. _Aquarium: https://github.com/aquarist-labs/aquarium

Installation
~~~~~~~~~~~~

.. code:: bash

    $ python3 -m pip install git+https://github.com/aquarist-labs/aetcd3/@aquarium#egg=aetcd3

Basic usage
~~~~~~~~~~~


.. code:: python

    import aetcd3

    etcd = aetcd3.client()
    await etcd.get('foo')
    await etcd.put('bar', 'doot')
    await etcd.delete('bar')

    # locks
    lock = etcd.lock('thing')
    await lock.acquire()
    # do something
    await lock.release()

    async with etcd.lock('doot-machine') as lock:
        # do something

    # transactions
    await etcd.transaction(
        compare=[
            etcd.transactions.value('/doot/testing') == 'doot',
            etcd.transactions.version('/doot/testing') > 0,
        ],
        success=[
            etcd.transactions.put('/doot/testing', 'success'),
        ],
        failure=[
            etcd.transactions.put('/doot/testing', 'failure'),
        ],
    )

    # watch key
    watch_count = 0
    events_iterator, cancel = await etcd.watch("/doot/watch")
    async for event in events_iterator:
        print(event)
        watch_count += 1
        if watch_count > 10:
            await cancel()

    # watch prefix
    watch_count = 0
    events_iterator, cancel = await etcd.watch_prefix("/doot/watch/prefix/")
    async for event in events_iterator:
        print(event)
        watch_count += 1
        if watch_count > 10:
            await cancel()

    # receive watch events via callback function
    def watch_callback(event):
        print(event)

    watch_id = await etcd.add_watch_callback("/anotherkey", watch_callback)

    # cancel watch
    await etcd.cancel_watch(watch_id)

    # receive watch events for a prefix via callback function
    def watch_callback(event):
        print(event)

Acknowledgements
~~~~~~~~~~~~~~~~

This project is a fork of `etcd3aio`_, which itself is a fork
of `python-etcd3`_. `python-etcd3` was originally written by `kragniz`_. `asyncio` suppport
was contributed by `hron`_ and based on the previous work by `gjcarneiro`_. Kudos to all
the people involved in the projects.

.. _grpclib: https://github.com/vmagamedov/grpclib
.. _etcd3aio: https://github.com/hron/etcd3aio
.. _python-etcd3: https://github.com/kragniz/python-etcd3
.. _kragniz: https://github.com/kragniz
.. _hron: https://github.com/hron
.. _gjcarneiro: https://github.com/gjcarneiro
