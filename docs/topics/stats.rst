.. _topics-stats:

================
Stats Collection
================

Scrapy provides a convenient facility for collecting stats in the form of
key/values, where values are often counters. The facility is called the Stats
Collector, and can be accessed through the :attr:`~scrapy.crawler.Crawler.stats`
attribute of the :ref:`topics-api-crawler`, as illustrated by the examples in
the :ref:`topics-stats-usecases` section below.

However, the Stats Collector is always available, so you can always import it
in your module and use its API (to increment or set new stat keys), regardless
of whether the stats collection is enabled or not. If it's disabled, the API
will still work but it won't collect anything. This is aimed at simplifying the
stats collector usage: you should spend no more than one line of code for
collecting stats in your spider, Scrapy extension, or whatever code you're
using the Stats Collector from.

Another feature of the Stats Collector is that it's very efficient (when
enabled) and extremely efficient (almost unnoticeable) when disabled.

The Stats Collector keeps a stats table per open spider which is automatically
opened when the spider is opened, and closed when the spider is closed.

.. _topics-stats-usecases:

Common Stats Collector uses
===========================

Access the stats collector through the :attr:`~scrapy.crawler.Crawler.stats`
attribute. Here is an example of an extension that access stats:

.. code-block:: python

    class ExtensionThatAccessStats:
        def __init__(self, stats):
            self.stats = stats

        @classmethod
        def from_crawler(cls, crawler):
            return cls(crawler.stats)

.. skip: start

Set stat value:

.. code-block:: python

    stats.set_value("hostname", socket.gethostname())

Increment stat value:

.. code-block:: python

    stats.inc_value("custom_count")

Set stat value only if greater than previous:

.. code-block:: python

    stats.max_value("max_items_scraped", value)

Set stat value only if lower than previous:

.. code-block:: python

    stats.min_value("min_free_memory_percent", value)

Get stat value:

.. code-block:: pycon

    >>> stats.get_value("custom_count")
    1

Get all stats:

.. code-block:: pycon

    >>> stats.get_stats()
    {'custom_count': 1, 'start_time': datetime.datetime(2009, 7, 14, 21, 47, 28, 977139)}

.. skip: end

Available Stats Collectors
==========================

Besides the basic :class:`StatsCollector` there are other Stats Collectors
available in Scrapy which extend the basic Stats Collector. You can select
which Stats Collector to use through the :setting:`STATS_CLASS` setting. The
default Stats Collector used is the :class:`MemoryStatsCollector`.

.. currentmodule:: scrapy.statscollectors

MemoryStatsCollector
--------------------

.. class:: MemoryStatsCollector

    A simple stats collector that keeps the stats of the last scraping run (for
    each spider) in memory, after they're closed. The stats can be accessed
    through the :attr:`spider_stats` attribute, which is a dict keyed by spider
    domain name.

    This is the default Stats Collector used in Scrapy.

    .. attribute:: spider_stats

       A dict of dicts (keyed by spider name) containing the stats of the last
       scraping run for each spider.

DummyStatsCollector
-------------------

.. class:: DummyStatsCollector

    A Stats collector which does nothing but is very efficient (because it does
    nothing). This stats collector can be set via the :setting:`STATS_CLASS`
    setting, to disable stats collect in order to improve performance. However,
    the performance penalty of stats collection is usually marginal compared to
    other Scrapy workload like parsing pages.

