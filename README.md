# zstream-rs ![Tests](https://github.com/51390/zstream-rs/actions/workflows/tests.yml/badge.svg)

An unopinionated implementation of gzip stream decoder/encoder for Rust.

## Why

Some encoder/decoder crates that provide `Read` Trait implementations exhibit a behavior of _eagerly_ trying to consume stream data from the underlying readers that are passed into them.
For use cases where the underlying stream data is not fully -- or not at all -- available for encoding/decoding immediately, this may lead to such implementations breaking with "corrupt deflate stream" errors or similar.

## How

This crate makes no assumptions about the underlying stream data availability, and will not call the libz functions for neither inflating nor deflating streaming data unless there are new bytes available. In other words, the `Read` implementations here should not yield errors by assuming that a stream has ended and is corrupted because an intermediate `read` call yields 0 bytes, when a subsequent call should yield the next bytes in the stream.
This allows a better flow control of the encoding / decoding process, in such cases where data may take a longer time to reach the component that is performing it, such as streaming data through unreliable networks.

One side-effect of the above, however, is that the Encoder implementation requires an explicit notification about when the data stream has ended, as it is not able to tell just by reading 0 bytes from the underlying stream. The `Encoder.finish` method is provided to allow for that.

## Documentation

TODO. For now you can check out the `decoder.rs` and `encoder.rs` source files.
