# ring

<!-- badges: start -->
[![Project Status: Active - The project has reached a stable, usable state and is being actively developed.](http://www.repostatus.org/badges/latest/active.svg)](http://www.repostatus.org/#active)
[![R build status](https://github.com/mrc-ide/ring/workflows/R-CMD-check/badge.svg)](https://github.com/mrc-ide/ring/actions)
[![codecov.io](https://codecov.io/github/mrc-ide/ring/coverage.svg?branch=master)](https://codecov.io/github/mrc-ide/ring?branch=master)
<!-- badges: end -->

> Ring buffers in R and C

Ring buffers (or circular buffers) are like arrays that seem circular; data is written and read in a first-in-first-out (FIFO) style, but once allocated subsequent writes do not cause memory allocations.  Circular buffers are useful for collecting and processing data streams or for queues (with a fixed maximum size).  I use them to implement a solver for delay differential equations in [dde](https://github.com/mrc-ide/dde).

This package provides two implementations of ring buffers:

1. `ring_buffer_env`: a pure R ring buffer implemented as double linked list (using environments) that is genuinely a ring.
2. `ring_buffer_bytes`: a ring buffer implemented as an array of bytes with a pair of pointers (in C).  Each "element" of the array can be one or more bytes, but each must have a fixed size.  There are two convenience interfaces:
  * `ring_buffer_bytes_translate`: for cases where a raw->R and R->raw translation functions are provided
  * `ring_buffer_bytes_typed` for cases where each element should correspond to a fixed-length vector of one of R's core numeric-ish types (`logical`, `integer`, `numeric`, `complex`)

Both buffer types can be used from R, and the code underlying `ring_buffer_bytes` can also be used compiled code in other packages using R's `LinkingTo:` support.  A common set of methods is provided, though these do differ in some details.

Both buffer types will refuse to underflow (return elements beyond those that have been written).  The behaviour on overflow can be controlled:

* overwrite old data: (the default), for a fixed-memory FIFO buffer
* grow buffer: expand the buffer geometrically to fit required elements (which will require additional memory allocations and copies)
* throw error: refuse to overflow

Depending on your application, these ring buffers may or may not be faster than manually copying data around in vectors.  The benefit of this package rather than doing it by hand is abstracting away a lot of subtle bookkeeping and keeping an inteface that is fairly high level.  However, for C applications, using `ring_buffer_bytes` via the C interface (using `LinkingTo`) is likely to be very fast as it avoids all copies.

See the [reference documentation](https://mrc-ide.github.io/ring) for details.

## Usage

See the vignette [online](https://mrc-ide.github.io/ring/vignettes/ring.html) (the vignette is not currently shipped with the pacakge as installed via `install_github`, though once the package in on CRAN it will be available with `code(vignette("ring"))`.

A second [vignette](https://mrc-ide.github.io/ring/vignettes/ring_applications.html) describes possible data structures using a ring buffer.

The reference documentation is also available [online](https://mrc-ide.github.io/ring), or from the package.

## License

MIT + file LICENSE © [Rich FitzJohn](https://github.com/richfitz).

Please note that this project is released with a [Contributor Code of Conduct](CONDUCT.md). By participating in this project you agree to abide by its terms.
