py-idstools for Snort3 
=================================

py-idstools is a collection of Python libraries for working with IDS
systems (typically Snort and Suricata). This repository is modified from 
the original to make it addapted to Snort3 records.  

Changes/new features from the original
--------------------------------------
- The file unified2.py was modified to include new record types. 
The new types were based on the official `file from snort3<https://github.com/snort3/snort3/blob/master/src/log/unified2.h>`.  
- The program u2_to_kafka.py is the first version of a program
to send records from Snort3 Unified2 files to kafka producer. 
This program converts u2 records to json for making Kafka 
produces them. 

Included Programs
-----------------
- rulecat - Basic Suricata rule management tool suitable as a
  replacement for for Oinkmaster and Pulled Pork.
- eve2pcap - Convert packets and payloads in eve logs to pcap.
- u2json - Convert unified2 files or spool directories to JSON.
- gensidmsgmap - Easily create a sid-msg.map file from rule files,
  directories or a rule tarball.
- dumpdynamicrules - Helper for dumping Snort SO dynamic rule stubs.
- u2eve - Convert unified2 files to EVE compatible JSON.


Library Features
----------------

- Snort/Suricata unified2 log file parsing.
- Continuous unified2 directory spool reading with bookmarking.
- Snort/Suricata rule parser.
- Parser and lookup maps for classification.config.
- Parser and lookup maps for gen-msg.map and sid-msg.map.

Requirements
------------

- Python 3.6 or newer.
- Currently only tested on Linux.

Installation
------------

Latest Release (Recommended)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

    pip3 install idstools


Latest from Git
~~~~~~~~~~~~~~~

    pip install https://github.com/jasonish/py-idstools/archive/master.zip

Manually
~~~~~~~~

The idstools programs do not have to be installed to be used, they can
be executable directly from the archive directory::

  ./bin/idstools-rulecat

Or to install manually::

  python setup.py install

Examples
--------

Reading a Unified2 Spool Directory
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following code snippet will "tail" a unified log directory
returning each record as a dict-like object::

  from idstools import unified2

  reader = unified2.SpoolRecordReader("/var/log/snort",
      "unified2.log", follow=True)
  for record in reader:
      if isinstance(record, unified2.Event):
          print("Event:")
      elif isinstance(record, unified2.Packet):
          print("Packet:")
      elif isinstance(record, unified2.ExtraData):
          print("Extra-Data:")
      print(record)

See the `idstools unified2
<http://idstools.readthedocs.io/en/latest/unified2.html>`_
documentation for more information on read and parsing unified2 files.

Parse Suricata/Snort Rules
~~~~~~~~~~~~~~~~~~~~~~~~~~

The following code snippet will parse all the rules in a rule file::

  from idstools import rule

  for rule in rule.parse_file(sys.argv[1]):
      print("[%d:%d:%d] %s" % (
          rule.gid, rule.sid, rule.rev, rule.msg))

In addition to parsing `files
<http://idstools.readthedocs.io/en/latest/apidoc/idstools.rule.html#idstools.rule.parse_file>`_,
`file objects
<http://idstools.readthedocs.io/en/latest/apidoc/idstools.rule.html#idstools.rule.parse_fileobj>`_
and `strings
<http://idstools.readthedocs.io/en/latest/apidoc/idstools.rule.html#idstools.rule.parse>`_
containing individual rules can be parsed.

Update Suricata Rules
~~~~~~~~~~~~~~~~~~~~~

The following command will update your Suricata rules with the latest
Emerging Threats Open ruleset for the version of Snort you have
installed::

  idstools-rulecat -o /etc/suricata/rules

See the `idstools-rulecat documentation
<http://idstools.readthedocs.io/en/latest/tools/rulecat.html>`_ for
more examples and options.

Documentation
-------------

Further documentation is located at http://idstools.readthedocs.org.

