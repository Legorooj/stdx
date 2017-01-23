<a id="list"></a>
# `stdx` - The missing batteries of Rust

New to Rust and don't yet know what crates to use?
[**stdx has the best crates**](#about-stdx).

Current revision:  `stdx` 0.115.0-rc, for Rust 1.15, February 2, 2017.

| Feature                        | Crate                     |                    |
|--------------------------------|---------------------------|--------------------|
| Bitfields                      | [`bitflags = "0.7.0"`]    | [📖][d-bitflags]    |
| Byte order conversion          | [`byteorder = "1.0.0"`]   | [📖][d-byteorder]   |
| Date and time                  | [`chrono = "0.2.25"`]     | [📖][d-chrono]      |
| Command-line argument parsing  | [`clap = "2.20.0"`]       | [📖][d-clap]        |
| Error handling                 | [`error-chain = "0.8.1"`] | [📖][d-error-chain] |
| JSON                           | [`json = "0.11.5"`]       | [📖][d-json]        |
| Global initialization          | [`lazy_static = "0.2.2"`] | [📖][d-lazy_static] |
| C interop                      | [`libc = "0.2.18"`]       | [📖][d-libc]        |
| Logging                        | [`log = "0.3.6"`]         | [📖][d-log]         |
| Multidimensional arrays        | [`ndarray = "0.7.2"`]     | [📖][d-ndarray]     |
| Big, rational, complex numbers | [`num = "0.1.36"`]        | [📖][d-num]         |
| Random numbers                 | [`rand = "0.3.15"`]       | [📖][d-rand]        |
| Parallel iteration             | [`rayon = "0.6.0"`]       | [📖][d-rayon]       |
| Regular expressions            | [`regex = "0.2.1"`]       | [📖][d-regex]       |
| HTTP client                    | [`reqwest = "0.3.0"`]     | [📖][d-reqwest]     |
| Serialization                  | [`serde = "0.9.0-rc2"`]   | [📖][d-serde]       |
| Temporary directories          | [`tempdir = "0.3.5"`]     | [📖][d-tempdir]     |
| Configuration files            | [`toml = "0.2.1"`]        | [📖][d-toml]        |
| URLs                           | [`url = "1.3.0"`]         | [📖][d-url]         |

[`bitflags = "0.7.0"`]: #bitflags
[`byteorder = "1.0.0"`]: #byteorder
[`chrono = "0.2.25"`]: #chrono
[`clap = "2.20.0"`]: #clap
[`error-chain = "0.8.1"`]: #error-chain
[`json = "0.11.5"`]: #json
[`lazy_static = "0.2.2"`]: #lazy_static
[`libc = "0.2.18"`]: #libc
[`log = "0.3.6"`]: #log
[`ndarray = "0.7.2"`]: #ndarray
[`num = "0.1.36"`]: #num
[`rand = "0.3.15"`]: #rand
[`rayon = "0.6.0"`]: #rayon
[`regex = "0.2.1"`]: #regex
[`reqwest = "0.3.0"`]: #reqwest
[`serde = "0.9.0-rc2"`]: #serde
[`tempdir = "0.3.5"`]: #tempdir
[`toml = "0.2.1"`]: #toml
[`url = "1.3.0"`]: #url

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="bitflags"></a>
### `bitflags = "0.7.0"` &emsp; [📖][d-bitflags]

[d-bitflags]: https://docs.rs/bitflags/0.7.0/bitflags/

The only thing this crate does is export the
[`bitflags!`](http://doc.rust-lang.org/bitflags/bitflags/macro.bitflags!.html#example)
macro, but it's a heckuva-useful macro. `bitflags!` produces typesafe
bitmasks, types with named values that are efficiently packed together
as bits to express sets of options.

**Example**: [`examples/bitflags.rs`]

[`examples/bitflags.rs`]: examples/bitflags.rs

```rust
#[macro_use]
extern crate bitflags;

bitflags! {
    flags Flags: u32 {
        const FLAG_A       = 0b00000001,
        const FLAG_B       = 0b00000010,
        const FLAG_C       = 0b00000100,
        const FLAG_ABC     = FLAG_A.bits
                           | FLAG_B.bits
                           | FLAG_C.bits,
    }
}

fn main() {
    let e1 = FLAG_A | FLAG_C;
    let e2 = FLAG_B | FLAG_C;
    assert_eq!((e1 | e2), FLAG_ABC);   // union
    assert_eq!((e1 & e2), FLAG_C);     // intersection
    assert_eq!((e1 - e2), FLAG_A);     // set difference
    assert_eq!(!e2, FLAG_A);           // set complement
}
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="byteorder"></a>
### `byteorder = "1.0.0"` &emsp; [📖][d-byteorder]

[d-byteorder]: https://docs.rs/byteorder/1.0.0/byteorder/

Functions for converting between numbers and bytes, in
little-endian, or big-endian orders.

**Example**: [`example/byteorder.rs`]

[`examples/byteorder.rs`]: examples/byteorder.rs

```rust
extern crate byteorder;

use std::io::Cursor;
use byteorder::{BigEndian, ReadBytesExt};
use byteorder::{LittleEndian, WriteBytesExt};

fn main() {
    // Read unsigned 16 bit big-endian integers from a Read type:
    let mut rdr = Cursor::new(vec![2, 5, 3, 0]);
    // Note that we use type parameters to indicate which kind of byte
    // order we want!
    assert_eq!(517, rdr.read_u16::<BigEndian>().unwrap());
    assert_eq!(768, rdr.read_u16::<BigEndian>().unwrap());

    // Write unsigned 16 bit little-endian integers to a Write type:
    let mut wtr = vec![];
    wtr.write_u16::<LittleEndian>(517).unwrap();
    wtr.write_u16::<LittleEndian>(768).unwrap();
    assert_eq!(wtr, vec![5, 2, 0, 3]);
}
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="chrono"></a>
### `chrono = "0.2.25"` &emsp; [📖][d-chrono]

[d-chrono]: https://docs.rs/chrono/0.2.25/chrono/

Date and time types.

**Example**: [`examples/chrono.rs`]

[`examples/chrono.rs`]: examples/chrono.rs

```rust
extern crate chrono;
use chrono::*;

fn main() {
    let local: DateTime<Local> = Local::now();
    let utc: DateTime<UTC> = UTC::now();

    let dt = UTC.ymd(2014, 11, 28).and_hms(12, 0, 9);

    assert_eq!((dt.year(), dt.month(), dt.day()), (2014, 11, 28));
    assert_eq!((dt.hour(), dt.minute(), dt.second()), (12, 0, 9));

    assert_eq!(dt.format("%Y-%m-%d %H:%M:%S").to_string(), "2014-11-28 12:00:09");
    assert_eq!(dt.format("%a %b %e %T %Y").to_string(), "Fri Nov 28 12:00:09 2014");

    assert_eq!(format!("{}", dt), "2014-11-28 12:00:09 UTC");
}
```

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="clap"></a>
### `clap = "2.20.0"` &emsp; [📖][d-clap]

[d-clap]: https://docs.rs/clap/2.20.0/clap/

Clap is a command line argument parser that is easy to
use and is highly configurable.

**Example**: [`examples/clap.rs`]

[`examples/clap.rs`]: examples/clap.rs

```rust
extern crate clap;
use clap::{Arg, App, SubCommand};
 
fn main() {
    let app = App::new("My Super Program")
        .version("1.0")
        .author("Kevin K. <kbknapp@gmail.com>")
        .about("Does awesome things")
        .arg(Arg::with_name("config")
             .short("c")
             .long("config")
             .value_name("FILE")
             .help("Sets a custom config file")
             .takes_value(true))
        .arg(Arg::with_name("INPUT")
             .help("Sets the input file to use")
             .required(true)
             .index(1))
        .subcommand(SubCommand::with_name("test")
                    .about("controls testing features")
                    .arg(Arg::with_name("debug")
                         .short("d")
                         .help("print debug information verbosely")));

    // Parse the command line arguments
    let matches = app.get_matches();
 
    let config = matches.value_of("config").unwrap_or("default.conf");
    let input = matches.value_of("INPUT").unwrap();

    // Handle subcommands
    match matches.subcommand() {
        ("clone",  Some(sub_matches)) => {
            if matches.is_present("d") {
                // ...
            }
        },
        ("push",   Some(sub_matches)) => {},
        ("commit", Some(sub_matches)) => {},
        _ => {},
    } 
}
```

**Alternatives**: [`docopt`]

[`docopt`]: https://docs.rs/docopt

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="error-chain"></a>
### `error-chain = "0.8.1"` &emsp; [📖][d-error-chain]

[d-error-chain]: https://docs.rs/error-chain/0.8.1/error_chain/

Rust programs that handle errors consistently are reliable programs.
Even after one understands [error handling] in Rust, it can be
difficult to grasp and implement its best practices. `error-chain`
helps you define your own error type that works with the `?` operator
to make error handling in Rust simple and elegant.

[error handling]: https://rust-lang.github.io/book/ch09-00-error-handling.html

**Example**: [`examples/error-chain.rs`]

[`examples/error-chain.rs`]: examples/error-chain.rs

```rust
#![allow(unused)]

// `error_chain!` can recurse deeply
#![recursion_limit = "1024"]

#[macro_use]
extern crate error_chain;

// We'll put our errors in an `errors` module, and other modules in
// this crate will `use errors::*;` to get access to everything
// `error_chain!` creates.
mod errors {
    // Create the Error, ErrorKind, ResultExt, and Result types
    error_chain! { }
}

use errors::*;

fn main() {
    if let Err(ref e) = run() {
        use ::std::io::Write;
        let stderr = &mut ::std::io::stderr();
        let errmsg = "Error writing to stderr";

        writeln!(stderr, "error: {}", e).expect(errmsg);

        for e in e.iter().skip(1) {
            writeln!(stderr, "caused by: {}", e).expect(errmsg);
        }

        // The backtrace is not always generated. Try to run this example
        // with `RUST_BACKTRACE=1`.
        if let Some(backtrace) = e.backtrace() {
            writeln!(stderr, "backtrace: {:?}", backtrace).expect(errmsg);
        }

        ::std::process::exit(1);
    }
}

// Most functions will return the `Result` type, imported from the
// `errors` module. It is a typedef of the standard `Result` type
// for which the error type is always our own `Error`.
fn run() -> Result<()> {
    use std::fs::File;
    use std::env;

    // Use chain_err to attach your own context to errors
    File::open("my secret file")
        .chain_err(|| "unable to open my secret file")?;

    // Use the `bail!` macro to return an error Result, ala `println!`
    bail!("giving up");

    Ok(())
}
```

**Alternatives**: [`quick-error`]

[`quick-error`]: https://docs.rs/quick-error/1.1.0/quick_error/

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="json"></a>
### `json = "0.11.5"` &emsp; [📖][d-json]

[d-json]: https://docs.rs/json/0.11.5/json/

Access to [JSON], the "JavaScript Object Notation" format,
widely used for transmission and storage of data on the Internet.
This crate can be used for reading, writing, and manipulation
of arbitrary JSON; for simple serialization to Rust data structures,
use [`serde`](#serde) and `serde_json`.

[JSON]: http://json.org/

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="lazy_static"></a>
### `lazy_static = "0.2.2"` &emsp; [📖][d-lazy_static]

[d-lazy_static]: https://docs.rs/env_logger/0.2.2/env_logger/

Global state is one of those things Rust doesn't do so well. In
particular there is no ['life before
main'](https://isocpp.org/wiki/faq/ctors#static-init-order) in Rust,
so it's not possible to write a programmatic constructor for a global
value that will be run at startup. Instead, Rust prefers lazy
execution for global initialization, and the `lazy_static!`
macro does just that.

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="libc"></a>
### `libc = "0.2.18"` &emsp; [📖][d-libc]

[d-libc]: https://docs.rs/libc/0.2.18/libc/

If you need to talk to foreign code, you need this crate. It exports C
type and function definitions appropriate to each target platform Rust
supports. It defines the standardized C features that are common
across all platforms as well as non-standard features specific to the
platform C libraries. For more platform-specific FFI definitions
see [`nix`] and [`winapi`].

**Supplemental crates**: [`nix`], [`winapi`]

[`nix`]: https://docs.rs/nix
[`winapi`]: https://docs.rs/winapi

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="log"></a>
### `log = "0.3.6"` &emsp; [📖][d-log]

[d-log]: https://docs.rs/log/0.3.6/log/

The most common way to perform basic logging in Rust, with the
`error!`, `warn!`, `info!`, and `debug!` macros. It is often
combined with the `env_logger` crate to get logging to the
console, controlled by the `RUST_LOG` environment variable.
This is the traditional logging crate used by `rustc`, and
its functionality was once built in to the language.

**Supplemental crates**: [`env_logger = "0.4.0"`]

[`env_logger = "0.4.0"`]: https://docs.rs/env_logger/0.4.0/env_logger/

**Alternatives**: [`slog`], [`log4rs`]

[`slog`]: https://docs.rs/slog
[`log4rs`]: https://docs.rs/log4rs

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="ndarray"></a>
### `ndarray = "0.7.2"` &emsp; [📖][d-ndarray]

[d-ndarray]: https://docs.rs/ndarray/0.7.2/ndarray/

The ndarray crate provides an N-dimensional container for general
elements and for numerics. The multidimensional array, otherwise known
as a "matrix", is a core data structure for numerical applications,
and Rust does not have one in the language or standard library.

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="num"></a>
### `num = "0.1.36"` &emsp; [📖][d-num]

[d-num]: https://docs.rs/num/0.1.36/num/

Big integers, rational numbers, complex numbers, and numeric
traits. This is a rust-lang crate that has persisted through Rust's
evolution but is somewhat unloved.

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="rand"></a>
### `rand = "0.3.15"` &emsp; [📖][d-rand]

[d-rand]: https://docs.rs/rand/0.3.15/rand/

Random number generators. The defaults are cryptographically strong.

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="rayon"></a>
### `rayon = "0.6.0"` &emsp; [📖][d-rayon]

[d-rayon]: https://docs.rs/rayon/0.6.0/rayon/

When people say that Rust makes parallelism easy, this
is why. Rayon provides parallel iterators that make
expressing efficient parallel operations simple
and foolproof.

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="regex"></a>
### `regex = "0.2.1"` &emsp; [📖][d-regex]

[d-regex]: https://docs.rs/regex/0.2.1/regex/

Rust's regular expressions are [fast], like Rust is fast. Part of
their power comes from a careful design that disallows back-references
and arbitrary lookahead, creating predictable worst-case performance.

[fast]: http://blog.burntsushi.net/ripgrep/

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="reqwest"></a>
### `reqwest = "0.3.0"` &emsp; [📖][d-reqwest]

[d-reqwest]: https://docs.rs/reqwest/0.3.0/reqwest/

A simple HTTP and HTTPS client.

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="serde"></a>
### `serde = "0.9.0-rc2"` &emsp; [📖][d-serde]

[d-serde]: https://docs.rs/serde/0.9.0-rc2/serde/

Serialization and deserialization of Rust datastructures is fast
and easy using the `serde` serialization framework. Simply
tag your data structures with `#[derive(Serialize, Deserialize)]`
and serde will automatically convert them between formats like
JSON, TOML, YAML, and more. To best understand serde, read
its documentation at [serde.rs].

**Supplemental crates**: [`serde_derive = "0.9.0-rc2"`],
                         [`serde_json = "0.9.0-rc1"`],
                         [`toml = "0.2.1"`]

**Alternatives**: [`rustc-serialize`]

[serde.rs]: https://serde.rs/
[`rustc-serialize`]: https://docs.rs/rustc-serialize
[`serde_derive = "0.9.0-rc2"`]: https://docs.rs/serde_derive/0.9.0-rc2/serde_derive
[`serde_json = "0.9.0-rc1"`]: https://docs.rs/serde_json/0.9.0-rc1/serde_json
[`toml = "0.2.1"`]: https://docs.rs/toml/0.2.1/toml

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="tempdir"></a>
### `tempdir = "0.3.5"` &emsp; [📖][d-tempdir]

[d-tempdir]: https://docs.rs/tempdir/0.3.5/tempdir/

The most common way to create temporary directories in Rust,
this crate was once part of the standard library.

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="toml"></a>
### `toml = "0.2.1"` &emsp; [📖][d-toml]

[d-toml]: https://docs.rs/toml/0.2.1/toml/

[TOML](https://github.com/toml-lang/toml) is a common format for
configuration files, like [Cargo.toml]. It's easy on the eyes, simple
to parse, and serializes from Rust types with [`serde`](#serde).

[Cargo.toml]: http://doc.crates.io/manifest.html

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


<a id="url"></a>
### `url = "1.3.0"` &emsp; [📖][d-url]

[d-url]: https://docs.rs/url/1.3.0/url/

The Rust URL parser and type, originally created
for [Servo].

[Servo]: https://servo.org

&nbsp;&NewLine;&nbsp;&NewLine;&nbsp;&NewLine;


## About `stdx`

Rust has a lovely and portable standard library, but it is not
featureful enough to write software of any great
sophistication. Compared to common platforms including Java, Python,
and Go, Rust's standard library is small.

In Rust, the libraries we use for even simple tasks live and evolve on
[crates.io]. This affords the Rust community freedom to experiment -
discovering the Rustiest solutions to even common problems can take
quite some iteration - but it also means that we're in for a slow
evolutionary process to converge around the best of those solutions. In
the meantime, you just have to know which crates to use for what.

`stdx` contains some of the most important crates in Rust. I mean
it. If Rust had a more expansive standard library, many of the `stdx`
crates would be in it, or at least the features they provide. Many of
the crates of `stdx` are maintained by the same authors as the Rustard
standard library, and they are designed to be ideomatic and
interoperable. These are core elements of the crates ecosystem that
all Rusticians should be aware of.

[crates.io]: https://www.crates.io

## How to use `stdx`

`stdx` is primarily a teaching tool. New and old Rust programmers
alike will get the most from it by digesting [the list](#list) of
`stdx` crates, each entry of which links to a descrption of the crate
along with _an example if its basic use_.

These examples are full working source and are intended to get you
up and running with any of the `stdx` crates _immediately_. Just
copy the crate name and version exactly as written into the `dependencies`
section of your `Cargo.toml` like so:

```toml
[dependencies]
bitflags = "0.7.0"
```

Then copy the full example into your `examples` directory, like
so

**Example**: [`examples/bitflags.rs`]

[`examples/bitflags.rs`]: examples/bitflags.rs

```rust
#[macro_use]
extern crate bitflags;

bitflags! {
    flags Flags: u32 {
        const FLAG_A       = 0b00000001,
        const FLAG_B       = 0b00000010,
        const FLAG_C       = 0b00000100,
        const FLAG_ABC     = FLAG_A.bits
                           | FLAG_B.bits
                           | FLAG_C.bits,
    }
}

fn main() {
    let e1 = FLAG_A | FLAG_C;
    let e2 = FLAG_B | FLAG_C;
    assert_eq!((e1 | e2), FLAG_ABC);   // union
    assert_eq!((e1 & e2), FLAG_C);     // intersection
    assert_eq!((e1 - e2), FLAG_A);     // set difference
    assert_eq!(!e2, FLAG_A);           // set complement
}
```

Then execute the following:

```sh
cargo run --example fitflags
```

And suddenly you are a slightly-experienced user of that crate.
Now click on the [📖][d-bitflags] icon to get the rest of the story
on that crate.

Convinced? [Go check out that list](#list).


## Why use `stdx`?

As a learning tool, I hope the benefit will be evident from a straight
read-through. But `stdx`, and tools like it, may provide important
benefits to users in the future.

To be clear, `stdx` is experimental. A lot of the below is
speculative.

`stdx` provides assurances that the versions of crates it specifes
work together correctly in a wide variety of configurations. Today
those assurances are few, but they will grow. And these types of
assurances will become increasingly valuable to Rust.

As of now, the only validation `stdx` provides is that the exact
versions of the `stdx` crates resolve correctly by Cargo, and that
they build on Linux and Windows. That is already beneficial by
uncovering problematic combinations and incorrect semver
specifications. Here are some other assurances that `stdx` will
enable:

* Additional integration test cases between the `stdx` crates
* Testing of all `stdx` crates' own test suites using the `stdx` version lock
* Testing on all tier 1 platforms
* Testing on tier 2 platforms
* Enforcement and coverage of `serde` features and interop
* Enforcement of other compile-time feature standards
* `stdx` as version lock - you don't even have to call into it. Just
  link to it and it locks down a chunk of your crate graph to
  known-good combinaitons.
* Ecosystem wide testing using `stdx` version lock - eventually we
  will be able to say which creates are known to work correctly
  with `stdx`.
* The more people use the `stdx` version lock the more assurance they
  get they get. This plays into future Rusts LTS directions.

By applying high quality standards to a small selection of critical
crates we can create a high degree of confidence in a larger core of
the Rust ecosystem.


## Selection criteria

The criteria for inclusion in `stdx` is conservative, and fuzzy. It's
mostly crates that are pretty super important, considering criteria
like

- portability
- quality
- conformance to conventions
- documentation
- interoperability with other crates
- reliability of maintainers
- de-facto adoption
- historical context and precedent

`stdx` is focused on core features, crates that are quintessentially
Rust and relied on by many Rust programs. It is intentionally
limited for the sake of simplicity and ease of comprehension.

All crates must work on Rust's tier-1 platforms, currently x86 Linux,
OS X, and Windows.


## License

`stdx` and the crates it links to are licensed under various
[permissive, BSD-like][perm] licenses. In lay-terms these licenses
allow their code to be used and distributed freely, and are compatible
with [Rust's own license (MIT/Apache 2)][rustlice].

`stdx` itself is dual MIT/Apache 2 licensed, like Rust, and the
copyright is owned by its contributors.

[perm]: https://en.wikipedia.org/wiki/Permissive_free_software_licence
[rustlice]: https://github.com/rust-lang/rust/blob/master/COPYRIGHT

