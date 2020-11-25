# aHash     ![Build Status](https://img.shields.io/github/workflow/status/tkaitchuck/ahash/Rust) ![Licence](https://img.shields.io/crates/l/ahash) ![Downloads](https://img.shields.io/crates/d/ahash) 

AHash is the [fastest](https://github.com/tkaitchuck/aHash/blob/master/compare/readme.md#Speed), 
[DOS resistant hash](https://github.com/tkaitchuck/aHash/wiki/How-aHash-is-resists-DOS-attacks) currently available in Rust.
AHash is intended *exclusively* for use in in-memory hashmaps. 

AHash's output is of [high quality](https://github.com/tkaitchuck/aHash/blob/master/compare/readme.md#Quality) but aHash is **not** a cryptographically secure hash.

## Design

Because AHash is a keyed hash, each map will produce completely different hashes, which cannot be predicted without knowing the keys.
[This prevents DOS attacks where an attacker sends a large number of items whose hashes collide that get used as keys in a hashmap.](https://github.com/tkaitchuck/aHash/wiki/How-aHash-is-resists-DOS-attacks)

This also avoids [accidentally quadratic behavior by reading from one map and writing to another.](https://accidentallyquadratic.tumblr.com/post/153545455987/rust-hash-iteration-reinsertion)

## Goals and Non-Goals

AHash does *not* have a fixed standard for its output. This allows it to improve over time. For example,
if any faster algorithm is found, aHash will be updated to incorporate the technique.
Similarly, should any flaw in aHash's DOS resistance be found, aHash will be changed to correct the flaw.

Because it does not have a fixed standard, different computers or computers on versions of the code will observe different hash values.
As such aHash not recommended for use other than in-memory maps. Specifically, aHash is not intended for network use or in applications which persist hashed values.
(In these cases `HighwayHash` would be a better choice)

Additionally, aHash is not intended to be cryptographly secure and should not be used as a MAC, or anywhere which requires a cryptographically secure hash.
(In these cases `SHA-3` would be a better choice)

## Usage

AHash is a drop in replacement for the default implementation of the `Hasher` trait. To construct a `HashMap` using aHash 
its hasher do the following:

```rust
use ahash::{AHasher, RandomState};
use std::collections::HashMap;

let mut map: HashMap<i32, i32, RandomState> = HashMap::default();
map.insert(12, 34);
```
For convinence wrappers called `AHashMap` and `AHashSet` are also provided.
These to the same thing with slightly less typing.
```rust
use ahash::AHashMap;

let mut map: AHashMap<i32, i32> = AHashMap::new();
map.insert(12, 34);
map.insert(56, 78);
```

## Flags

The aHash package has three flags:
* `std`: This enables features which require the standard library. (On by default) This includes generating random keys and providing the utility classes `AHashMap` and `AHashSet`.
* `compile-time-rng`: As an alternative to `std` when it is not available, this generates Random numbers for keys at compile time. This allows for DOS resistance even if there is no random number generator available at runtime (assuming the compiled binary is not public).
Note that this has the effect of making the output of the build non-deterministic. 
* `specialize`: This uses the specialization feature to provide optimized algorithms for primitive types. (This requires nightly)

**NOTE:** If neither `std` or `compile-time-rng` aHash will fall back on using the numeric value of memory addresses as a source of randomness.
This is somewhat strong if ALSR is turned on (it is by default) but for some embedded platforms where this is not available,
this will result in weak keys. As a result, it is strongly recommended to use `std` when it is available and `compile-time-rng` when developing for an embedded platform where `std` is not available.
(If both are enabled `std` will take precedence and `compile-time-rng` will have no effect.)


## Comparison with other hashers

A full comparison with other hashing algorithms can be found [here](https://github.com/tkaitchuck/aHash/blob/master/compare/readme.md)

![Hasher perfromance](https://docs.google.com/spreadsheets/d/e/2PACX-1vSK7Li2nS-Bur9arAYF9IfT37MP-ohAe1v19lZu5fd9MajI1fSveLAQZyEie4Ea9k5-SWHTff7nL2DW/pubchart?oid=1323618938&format=image)

For more a more representative performance comparison which includes the overhead of using a HashMap, see [HashBrown's benchmarks](https://github.com/rust-lang/hashbrown#performance)
as HashBrown now uses aHash as its hasher by default.

## Hash quality

AHash passes the full [SMHasher test suite](https://github.com/rurban/smhasher). 

The code to reproduce the result, and the full output [are checked into the repo](https://github.com/tkaitchuck/aHash/tree/master/smhasher).

## Additional FAQ

A separate FAQ document is maintained [here](https://github.com/tkaitchuck/aHash/blob/master/FAQ.md). 
If you have questions not covered there, open an issue [here](https://github.com/tkaitchuck/aHash/issues).

## License

Licensed under either of:

 * Apache License, Version 2.0, ([LICENSE-APACHE](LICENSE-APACHE) or http://www.apache.org/licenses/LICENSE-2.0)
 * MIT license ([LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT)

at your option.

## Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in the work by you, as defined in the Apache-2.0 license, shall be dual licensed as above, without any
additional terms or conditions.








