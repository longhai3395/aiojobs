.. _aiojobs-quickstart:

Quickstart
==========

.. currentmodule:: aiojobs


The library gives controlled way for scheduling background tasks.

Install the library:

.. code-block:: bash

   $ pip3 install aiojobs

The library API is pretty minimalistic: make a scheduler, spawn jobs,
close scheduler.

Instantiate a scheduler::

   import aiojobs

   scheduler = await aiojobs.create_scheduler()

Spawn a new job::

   await scheduler.spawn(coro())

At the end of program gracefuly close the scheduler::

   await scheduler.close()


Let's collect it altogether into very small but still functional example::

   import asyncio
   import aiojobs

   async def coro(timeout):
       await asyncio.sleep(timeout)

   async def main():
       scheduler = aiojobs.create_scheduler()
       for i in range(100):
           # spawn jobs
           await scheduler.spawn(coro(i/10))

       await asyncio.sleep(5.0)
       # not all scheduled jobs are finished at the moment

       # gracefuly close spawned jobs
       await scheduler.close()

   asyncio.get_event_loop().run_until_complete(main())


Our jobs are very simple :func:`asyncio.sleep` calls with variable
timeout -- pretty enough for demonstration.

Example schedules ``100`` jobs, every job takes from ``0`` to ``10``
seconds for its execution.

Next we waits for ``5`` seconds. Roughly half of scheduled jobs should
be finished already but ``50`` jobs are still active.

For closing them we calls ``await scheduler.close()``, the call sends
:exc:`asyncio.CancelledError` into every non-closed job to stop it.

That's pretty much it.


For more info about library design and principles read :ref:`aiojobs-intro`.

API reference is here: :ref:`aiojobs-api`.