Future work
===========

Short term
----------

- New feature: RPKI filtering/tagging (based on `rtrsub`_ and/or `rtrlib`_)
- New feature: selective prepending via BGP communities
- New feature: custom informative BGP communities
- Live tests: provide a skeleton to ease building of new live test scenarios.
- Add options for bgpq3 (sources)
- Doc: contributing section
- Templates: textual representation of configurations

Mid term
--------

- Split configuration in multiple files
- Doc: better documentation
- Doc: schema of data that can be used within J2 templates

Long term
---------

- New feature: routing policies based on RPSL import-via/export-via
- New feature: other BGP speakers support (OpenBGPD, ...)
- New feature: balance clients among *n* different configurations (for multiple processes - see `Scaling BIRD Routeservers <https://ripe73.ripe.net/presentations/115-e-bru-20161026-RIPE73-scaling-bird-routeservers-final.pdf>`_)

.. _rtrsub: https://github.com/job/rtrsub
.. _rtrlib: https://github.com/rtrlib/bird-rtrlib-cli

