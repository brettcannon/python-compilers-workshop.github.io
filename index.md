---
layout: default
---

# Python Compilers Workshop

*What:* A workshop to bring together folks working on different
approaches to **high-performance compilation for Python**, to share
experience, discuss common interests, and start sketching out a
roadmap for how the broader Python ecosystem can adapt to take maximal
advantage of these new implementations.

*When and where:* **July 11-12, 2016** in **Austin, Texas**,
co-located with the [SciPy 2016](http://scipy2016.scipy.org)
conference. (This is just before the main conference, and overlaps
with the SciPy tutorial days.)

*Announcements / discussion:*
[the python-compilers mailing list at python.org](https://mail.python.org/mailman/listinfo/python-compilers)

*Venue:* [AT&T Center](http://www.meetattexas.com/), room 107 on July
11, room 104 on July 12

*Who:* Open to the public; no registration fee. However
[RSVP's are appreciated](mailto:njs@pobox.com) for planning purposes,
and so that I can update the confirmed attendees list below. In
addition, we anticipate that we will have travel funding available for
open-source developers working on topically-relevant projects who
would otherwise find it difficult to attend. If this applies to you
then
[please get in touch as soon as possible](mailto:njs@pobox.com). (Note
that
[SciPy also has financial aid available](http://scipy2016.scipy.org/ehome/146062/388110/?&&),
with applications due April 22.)

*Organized by:* [Nathaniel Smith](https://vorpus.org)
([njs@pobox.com](mailto:njs@pobox.com))


## Motivation

There's an intense and growing interest in techniques for compiling
Python or near-Python languages to native code; a partial list
includes [PyPy](http://pypy.org/), [Numba](http://numba.pydata.org/),
[Pyston](https://github.com/dropbox/pyston),
[Cython](http://cython.org/), [Jython](http://www.jython.org/),
[Nuitka](http://nuitka.net/),
[Pythran](https://github.com/serge-sans-paille/pythran),
[Pyjion](https://github.com/Microsoft/Pyjion),
[Numexpr](https://github.com/pydata/numexpr),
[HOPE](www.cosmology.ethz.ch/research/software-lab/HOPE.html),
[GT-Py](https://software.intel.com/en-us/blogs/2016/03/22/gt-py-accelerating-numpy-programs-with-minimal-programming-effort)...

Now seems like a good time to compare notes! Plus there are lots of
questions that seem like they could benefit from some cross-project
collaboration. Our exact agenda will depend on participants' interests,
but here are some examples to give the flavor:

* If I wrap a C function using Cython/CFFI/SWIG/..., could there
  somehow be a way to expose the original C function to
  Numba/PyPy/Pyston/etc. to cut out the wrapper overhead? What about
  vice-versa: if I have a Python function that's been JIT-compiled and
  I pass it to some native code like
  [`scipy.optimize.fmin`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.fmin.html#scipy.optimize.fmin),
  could there be some way for the native code in `scipy` to call the
  JIT-compiled function directly without going through tuple
  packing/unpacking? Can Numba and Pyston benefit from PyPy's work on
  CFFI?

* Does it make sense to run Numba on PyPy or Pyston?

* The Python 2 vs. Python 3 split is very painful for anyone working
  on compilers/interpreters. What strategies for handling this have
  worked or not worked?

* A number of the compilers above can take code like

  ~~~python
  total = 0.0
  for num in range(10000):
      total += num
  ~~~

  and perform inlining, unboxing, etc. to compile it down to a tight
  native loop that far out-performs CPython. This is possible because
  they have intimate knowledge of built-in Python constructs like
  `float`, `int`, and `range`.  And a number of them have good enough
  CPython C API compatibility that they can -- or will soon be able to
  -- execute the same code, but using `numpy`:

  ~~~python
  import numpy as np
  total = 0.0
  for num in np.arange(10000):
      total += num
  ~~~

  But if you're using the CPython C API to call `numpy`, then you
  can't do unboxing/inlining/loop fusion/etc., because `numpy` objects
  are a total black box to the compiler, and so this `numpy`ified
  version of the loop will run at about the same speed in a
  state-of-the-art JIT as it would in CPython.

  The traditional way to solve this -- as seen in e.g. Numba or
  Numexpr -- is to give the compiler built-in knowledge of `numpy`
  types and operations, essentially reimplementing `numpy` inside each
  compiler. But this strategy has a number of obvious downsides in
  terms of duplicated effort, subtle incompatibilities, increased
  testing load for downstream projects, reduced ability to evolve and
  improve `numpy`'s semantics, and the potential need to then repeat
  the whole exercise for other projects like
  [`dynd`](http://libdynd.org/) (a `numpy` competitor) or
  `pandas`. And this question is becoming particularly urgent, since
  not only has Numba already started down this road, but Pyston and
  PyPy are both working on passing the `numpy` test suite right now
  and are likely to soon move from worrying about correctness to
  worrying about speed.

  Can we do better? Could there be some way to write a library like
  `numpy` so that a single codebase could simultaneously target
  CPython and the newer compilers, while achieving competitive speed
  in all cases? If so, what would it take to make that happen? If not,
  then what's the next-best alternative?


## Schedule

TBD -- probably we'll start with some talks from different projects to
outline their approaches and name some problems they're worrying
about, and then switch to unconference mode.


## A partial list of confirmed attendees

* Nathaniel Smith (NumPy)
* Kevin Modzelewski (Pyston)
* Marius Wachtler (Pyston)
* Maciej Fijalkowski (PyPy)
* Matti Picus (PyPy)
* Joel Akeret (HOPE)
* Robert Cohn (Intel)
* Anton Malakhov (Intel)
* Chi-Keung Luk (Intel, GT-Py)
* Jonathan Rocher


## Sponsors

We're grateful to our sponsors for helping make this event possible:

![Intel](/assets/intel-logo.png)


## Travel information

See the [SciPy 2016 web site](http://scipy2016.scipy.org/) for
suggestions on
[lodging](http://scipy2016.scipy.org/ehome/146062/332952/?&&),
[transportation](http://scipy2016.scipy.org/ehome/146062/332955/?&&),
and other travel details.


## Code of conduct

Workshop attendees are expected to adhere to the
[SciPy code of conduct](http://scipy2016.scipy.org/ehome/146062/388087/?&&).
Please report any violations or concerns to
[Nathaniel Smith](mailto:njs@pobox.com) or a member of SciPy staff.
