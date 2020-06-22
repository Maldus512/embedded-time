# embedded-time

`embedded-time` provides a comprehensive library for implementing abstractions over
hardware and work with _clocks_, _instants_, _durations_, _periods_, and _frequencies_ in a more intuitive way.
 
- `Clock` trait allowing abstraction of hardware timers for timekeeping.
- Work with time using _milliseconds_, _seconds_, _hertz_, etc. rather than _cycles_ or _ticks_.
- Includes example for the nRF52_DK board
- Conversion to/from core::time::Duration

## Motivation
The handling of time on embedded systems is generally much different than that of OSs. For instance, on an OS, the time is measured against an arbitrary epoch. Embedded systems generally don't know (nor do they care) what the *real* time is, but rather how much time has passed since the system has started.
 
### Drawbacks of the standard library types
#### Duration
- The storage is `u64` seconds and `u32` nanoseconds.
  - This is huge overkill and adds needless complexity beyond what is required (or desired) for embedded systems.
- Any read requires arithmetic to convert to the requested units
  - This is much slower than this project's implementation of what is analogous to a tagged union of time units.
#### Instant
- The `Instant` type requires `std`.

### Drawbacks of the [`time`](https://crates.io/crates/time) crate
The `time` crate is a remarkable library but isn't geared for embedded systems (although it does support a subset of features in `no_std` contexts). It suffers from some of the same drawbacks as the core::Duration type (namely the storage format) and the `Instant` struct dependency on `std`. It also adds a lot of functionally that would seldom be useful in an embedded context. For instance it has a comprehensive date/time formatting, timezone, and calendar support.

## Background
### What is an Instant?
In the Rust ecosystem, it appears to be idiomatic to call a `now()` associated function from an Instant type. There is generally no concept of a "Clock". I believe that using the `Instant` in this way is a violation of the *separation of concerns* principle. What is an `Instant`? Is it a time-keeping entity from which you read the current instant in time, or is it that instant in time itself. In this case, it's both.

As an alternative, the current instant in time is read from a **Clock**. The `Instant` read from the `Clock` has the same precision and width (inner type) as the `Clock`. Requesting the difference between two `Instant`s gives a `Duration` which can have different precision and/or width.

## License
This project is licensed under either of
- [Apache License, Version 2.0](https://github.com/time-rs/time/blob/master/LICENSE-Apache)
- [MIT license](https://github.com/time-rs/time/blob/master/LICENSE-MIT)

at your option.

Unless you explicitly state otherwise, any contribution intentionally submitted for inclusion in time by you, as defined in the Apache-2.0 license, shall be dual licensed as above, without any additional terms or conditions.