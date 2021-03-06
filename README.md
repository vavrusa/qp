qp tries and crit-bit tries
===========================

I have been working on radix trees / patricia tries / crit-bit tries
with a larger fan-out per branch to reduce lookup costs without
wasting memory.

My best solution so far is the "qp trie", short for quadbit popcount
patricia trie. (Nothing to do with cutie cupid dolls or Japanese
mayonnaise!) A qp trie is like a crit-bit trie (aka patricia trie)
except each branch is indexed by a quadbit (a nibble) at a time
instead of one bit. The array of sub-tries at a branch node is
compressed using the popcount trick to omit unused branches.

Based on a few benchmarks, qp tries have about 1/3 less memory
overhead of crit-bit tries, 1.3 words vs 2 words of overhead per item;
the average depth of a qp trie is about half that of a crit-bit trie;
and the overall speed of qp tries is about 10% faster than crit-bit
tries. The qp trie implementation is about 40% bigger.


usage
-----

Type `make test` or `make bench`. (You will need to use GNU make.)

There is a build option HAVE_SLOW_POPCOUNT which compiles the code to
use a hand-coded 16 bit popcount() instead of __builtin_popcount().
The makefile builds test-qs and bench-qs with this option; they are
otherwise the same as test-qp and bench-qp. HAVE_SLOW_POPCOUNT is very
effective with gcc; clang's popcount is about as quick as mine.


caveats
-------

The code has only been tested on 64-bit little endian machines. It
might work on 32-bit machines (provided the compilter supports 64 bit
integers) and probably won't work on a big-endian machine. It should
be easy to port by tweaking the struct bit-field layouts. Key strings
can be byte-aligned but values must be word-aligned; you can swap this
restriction (e.g. if you want to map from strings to integers) by
tweaking the struct layout and adjusting the check in Tset().


download
--------

You can clone or browse the repository from:

* git://dotat.at/qp.git
* <http://dotat.at/cgi/git/qp.git>
* <https://github.com/fanf2/qp.git>
* <https://git.csx.cam.ac.uk/x/ucs/u/fanf2/radish.git>


articles
--------

* [2015-10-04](https://github.com/fanf2/qp/blob/master/blog-2015-10-04.md)
	qp tries: smaller and faster than crit-bit tries

	A blog article / announcement.


roadmap
-------

* [Tbl.h][] [Tbl.c][]

	Abstract programming interface for tables with string keys and
	associated `void*` values. Intended to be shareable by multiple
	different implementations.

* [qp.h][] [qp.c][]

	My qp trie implementation. See qp.h for a longer description
	of where the data structure comes from.

* [cb.h][] [cb.c][]

	My crit-bit trie implementation. See cb.h for a description of
	how it differs from DJB's crit-bit code.

* [qp-debug.c][] [cb-debug.c][]

	Debug support code.

* [bench.c][] [bench-multi.pl][]

	Generic benchmark for Tbl.h implementations, and a benchmark
	driver for comparing different implementations.

* [test.c][] [test.pl][]

	Generic test harness for the Tbl.h API, and a perl reference
	implementation for verifying correctness.

* [test-gen.pl][] [test-once.sh][]

	Driver scripts for the test harness.


[Tbl.c]:          https://github.com/fanf2/qp/blob/HEAD/Tbl.c
[Tbl.h]:          https://github.com/fanf2/qp/blob/HEAD/Tbl.h
[bench-multi.pl]: https://github.com/fanf2/qp/blob/HEAD/bench-multi.pl
[bench.c]:        https://github.com/fanf2/qp/blob/HEAD/bench.c
[cb-debug.c]:     https://github.com/fanf2/qp/blob/HEAD/cb-debug.c
[cb.c]:           https://github.com/fanf2/qp/blob/HEAD/cb.c
[cb.h]:           https://github.com/fanf2/qp/blob/HEAD/cb.h
[qp-debug.c]:     https://github.com/fanf2/qp/blob/HEAD/qp-debug.c
[qp.c]:           https://github.com/fanf2/qp/blob/HEAD/qp.c
[qp.h]:           https://github.com/fanf2/qp/blob/HEAD/qp.h
[test-gen.pl]:    https://github.com/fanf2/qp/blob/HEAD/test-gen.pl
[test-once.sh]:   https://github.com/fanf2/qp/blob/HEAD/test-once.sh
[test.c]:         https://github.com/fanf2/qp/blob/HEAD/test.c
[test.pl]:        https://github.com/fanf2/qp/blob/HEAD/test.pl

---------------------------------------------------------------------------

Written by Tony Finch <dot@dotat.at>;
You may do anything with this. It has no warranty.
<http://creativecommons.org/publicdomain/zero/1.0/>
