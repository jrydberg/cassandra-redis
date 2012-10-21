# A Redis-like Data Structure Server, on top of Cassandra #

Why? For the lulz.


# Lists

Lists hold elements, ordered by the user.

http://thelastpickle.com/2012/08/18/Sorting-Lists-For-Humans/

# Sets

Two rows for a set: `<name>:add` and `<name>:remove`.

The column type is a composite of `(string, int)`.  The integer is
a generation counter, lets call it *GCNT*.

When adding an element to the set, create a column `(element, GCNT)`
where *GCNT* is +1 the highest *GCNT* for `element`found in the
`:remove` row.  The value of the column is a UUID that identifies the
transaction.

When removing an element from the set, create a column `(element,
GCNT)` in the `:remove` row.  The value of the column is a UUID that
identifies the transaction.

By doing reads and writes with a CL of `QUORUM` you may see if you're
in the middle of a race condition

Garbage is collected by re-writing columns in `:add` that has been
removed, with a low TTL.  The columns in `:remove` can be purged when
there's no corresponding column in `:add`.  This will reset *GCNT* for
that element.


# Sorted Sets

Maybe a combination of sets and lists?

