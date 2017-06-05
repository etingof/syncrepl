[![Documentation Status](http://readthedocs.org/projects/syncrepl-client/badge/?version=latest)](http://syncrepl-client.readthedocs.io/en/latest/?badge=latest)

# Oh No!

You really shouldn't be looking at this.  We're not ready yet!

# What is This?

syncrepl\_client is a Python module that makes LDAP Syncrepl easy to use.

LDAP Syncrepl allows you to keep up-to-date with an LDAP server, effectively in
real time, even without LDAP administrator credentials.

If your LDAP directory is used as the source of truth (or a delegate for the
soource of truth), this keeps you informed when something changes.
Callbacks—which you write—are triggered by this code when something happens.
You can then take appropriate action, such as by inserting into a queue or
sending a message over a message bus.

## What is Syncrepl?

Syncrepl (as described in [RFC 4533](https://datatracker.ietf.org/doc/rfc4533/)
is a standard which allows an LDAP server to keep clients in sync with itself.
The clients keep track of a "cookie", an opaque string that the server uses to
know how far behind the client is.  The LDAP server then "refreshes" the
client by sending details of new & changed entries, as well as information on
which entries have been deleted.  After the refresh is complete, the client is
able to keep a long-running connection open to the server, and receive notice
as soon as a change happens on the server.

Syncrepl is the way that OpenLDAP uses to implement replication (from a master
LDAP server to one or more slaves), but the client does not have to be an
OpenLDAP server.  In fact, because Syncrepl is layered on top of an ordinary
LDAP search, regular LDAP clients—even those with limited access—are able to
use Syncrepl to effectively be notified as soon as the results of their search
has changed.  This includes notification on:

* New entries that match your search filter.

* Entries being deleted.

* Entries, which used to match, no longer matching.  This is essentially the
  same as deletion (at least, it is when you are using a search filter).

* Existing entries having their attributes changed, including their DN.

The entries you see, and the changes made to them, are based on the
intersection of four things.

* The entries currently in the directory.

* Your access rights, as determined by your bind DN.

* Your search filter.

* Your list of requested attributes.

Thanks to the Syncrepl protocol, you don't have to worry about all of the
above.  The LDAP server handles the work of figuring out what you can see.

# Requirements

`syncrepl_client` has four requirements:

* Python 2.7, or Python 3.3+.

  If you use Python 2.7 or 3.3, you will also need
  [enum34](https://bitbucket.org/stoneleaf/enum34).

* An appropriate Python LDAP library:

  * For Python 2.7, [python-ldap 2.4.40](https://www.python-ldap.org) or later
    is needed.

  * For Python 3, [pyldap 2.4.40](https://github.com/pyldap/pyldap) or later is needed.

* A data store, large enough to store a copy of all the LDAP data received, and fast.

* An LDAP server which supports RFC 4533, and which is keeping track of changes.

Lots more details are available in [the
documentation](http://syncrepl-client.readthedocs.io/en/latest/requirements.html).

# How to Use

Although there is a fair bit of coding on your part (mainly in Step 1), this
code is (intentionally) pretty easy to use!  Over the life of your code, you
should do these four things:

1. Create a class which implements the methods defined in
   [BaseCallback](http://syncrepl-client.readthedocs.io/en/latest/callbacks.html#syncrepl_client.callbacks.BaseCallback).
   This is how you are notified of changes from the LDAP server.

2. In your main code, import
   [syncrepl\_client](http://syncrepl-client.readthedocs.io/en/latest/client.html)
   and instantiate a new
   [Syncrepl](http://syncrepl-client.readthedocs.io/en/latest/client.html#syncrepl_client.Syncrepl)
   object.  The instantiation will handle the connection and the search setup.

3. Call
   [poll](http://syncrepl-client.readthedocs.io/en/latest/client.html#syncrepl_client.Syncrepl.poll)
   until it returns `False`.  If you're running single-threaded, set `timeout`
   to some positive, non-zero value.  Call
   [please\_stop](http://syncrepl-client.readthedocs.io/en/latest/client.html#syncrepl_client.Syncrepl.please_stop)
   when you want to safely shut down, and then resume calling `poll` until it
   returns `False`.

4. Call
   [unbind](http://syncrepl-client.readthedocs.io/en/latest/client.html#syncrepl_client.Syncrepl.unbind).
   You're done!

Lots more details are available in [the
documentation](http://syncrepl-client.readthedocs.io/en/latest/requirements.html),
and see demo.py for a simple example.

# Copyright and License

The contents of this repository are copywrited according to the contents of the 
`AUTHORS` file.

The code is made available under the BSD 3-Clause License.

Other code is made available under the Creative Commons CC0 Public Domain Dedication.

Documentation is made available under the Creative Commons
Attribution-ShareAlike 4.0 International Public License (the CC BY-SA License).
Code contained within documentation is made available under both the BSD
3-Clause License, and the CC BY-SA License.

To identify the license for any particular file, refer to the contents of the
file.

The text of the BSD 3-Clause License is reproduced in the file `LICENSE.md`.
The text of the other licenses may be found in the file `LICENSE_others.md`.
Note that all three licenses are equally important, but are kept in a separate
files to aid GitHub's irepository license-detection mechanisms.
