`ord`
=====

`ord` is an index, block explorer, and command-line wallet. It is experimental
software with no warranty. See [LICENSE](LICENSE) for more details.

Ordinal theory imbues satoshis with numismatic value, allowing them to
be collected and traded as curios.

Ordinal numbers are serial numbers for satoshis, assigned in the order in which
they are mined, and preserved across transactions.

See [the docs](https://docs.ordinals.com) for documentation and guides.

See [the BIP](bip.mediawiki) for a technical description of the assignment and
transfer algorithm.

See [the project board](https://github.com/users/casey/projects/3/) for
currently prioritized issues.

See [milestones](https://github.com/ordinals/ord/milestones) to get a sense of
where the project is and where it's going.

Join [the Discord server](https://discord.gg/87cjuz4FYg) to chat with fellow
ordinal degenerates.

Donate
------

Ordinals is open-source and community funded. The current lead maintainer of
`ord` is [raphjaph](https://github.com/raphjaph/). Raph's work on `ord` is
entirely funded by donations. If you can, please consider donating!

The donation address is
[bc1q8kt9pyd6r27k2840l8g5d7zshz3cg9v6rfda0m248lva3ve5072q3sxelt](https://mempool.space/address/bc1q8kt9pyd6r27k2840l8g5d7zshz3cg9v6rfda0m248lva3ve5072q3sxelt).

This address is 2 of 4 multisig wallet with keys held by
[raphjaph](https://twitter.com/raphjaph),
[erin](https://twitter.com/realizingerin),
[rodarmor](https://twitter.com/rodarmor), and
[ordinally](https://twitter.com/veryordinally).

Bitcoin received will go towards funding maintenance and development of `ord`,
as well as hosting costs for [ordinals.com](https://ordinals.com).

Thank you for donating!

Wallet
------

`ord` relies on Bitcoin Core for private key management and transaction signing.
This has a number of implications that you must understand in order to use
`ord` wallet commands safely:

- Bitcoin Core is not aware of inscriptions and does not perform sat
  control. Using `bitcoin-cli` commands and RPC calls with `ord` wallets may
  lead to loss of inscriptions.

- `ord wallet` commands automatically load the `ord` wallet given by the
  `--wallet` option, which defaults to 'ord'. Keep in mind that after running
  an `ord wallet` command, an `ord` wallet may be loaded.

- Because `ord` has access to your Bitcoin Core wallets, `ord` should not be
  used with wallets that contain a material amount of funds. Keep ordinal and
  cardinal wallets segregated.

### Pre-alpha wallet migration

Alpha `ord` wallets are not compatible with wallets created by previous
versions of `ord`. To migrate, use `ord wallet send` from the old wallet to
send sats and inscriptions to addresses generated by the new wallet with `ord
wallet receive`.

Installation
------------

`ord` is written in Rust and can be built from
[source](https://github.com/ordinals/ord). Pre-built binaries are available on the
[releases page](https://github.com/ordinals/ord/releases).

You can install the latest pre-built binary from the command line with:

```sh
curl --proto '=https' --tlsv1.2 -fsLS https://ordinals.com/install.sh | bash -s
```

Once `ord` is installed, you should be able to run `ord --version` on the
command line.

Building
--------

On Debian and Ubuntu, `ord` requires `libssl-dev` when building from source:

```
sudo apt-get install libssl-dev
```

You'll also need Rust:

```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

To build `ord` from source:

```
git clone https://github.com/ordinals/ord.git
cd ord
cargo build --release
```

Once built, the `ord` binary can be found at `./target/release/ord`.

`ord` requires `rustc` version 1.67.0 or later. Run `rustc --version` to ensure you have this version. Run `rustup update` to get the latest stable release.

Contributing
------------

If you wish to contribute there are a couple things that are helpful to know. We
put a lot of emphasis on proper testing in the code base, with three broad
categories of tests: unit, integration and fuzz. Unit tests can usually be found at
the bottom of a file in a mod block called `tests`. If you add or modify a
function please also add a corresponding test. Integration tests try to test
end-to-end functionality by executing a subcommand of the binary. Those can be
found in the [tests](tests) directory. We don't have a lot of fuzzing but the
basic structure of how we do it can be found in the [fuzz](fuzz) directory.

We strongly recommend installing [just](https://github.com/casey/just) to make
running the tests easier. To run our CI test suite you would do:

```
just ci
```

This corresponds to the commands:

```
cargo fmt -- --check
cargo test --all
cargo test --all -- --ignored
```

Have a look at the [justfile](justfile) to see some more helpful recipes
(commands). Here are a couple more good ones:

```
just fmt
just fuzz
just doc
just watch ltest --all
```

If the tests are failing or hanging, you might need to increase the maximum
number of open files by running `ulimit -n 1024` in your shell before you run
the tests, or in your shell configuration.

We also try to follow a TDD (Test-Driven-Development) approach, which means we
use tests as a way to get visibility into the code. Tests have to run fast for that
reason so that the feedback loop between making a change, running the test and
seeing the result is small. To facilitate that we created a mocked Bitcoin Core
instance in [test-bitcoincore-rpc](./test-bitcoincore-rpc).

Syncing
-------

`ord` requires a synced `bitcoind` node with `-txindex` to build the index of
satoshi locations. `ord` communicates with `bitcoind` via RPC.

If `bitcoind` is run locally by the same user, without additional
configuration, `ord` should find it automatically by reading the `.cookie` file
from `bitcoind`'s datadir, and connecting using the default RPC port.

If `bitcoind` is not on mainnet, is not run by the same user, has a non-default
datadir, or a non-default port, you'll need to pass additional flags to `ord`.
See `ord --help` for details.

`bitcoind` RPC Authentication
-----------------------------

`ord` makes RPC calls to `bitcoind`, which usually requires a username and
password.

By default, `ord` looks a username and password in the cookie file created by
`bitcoind`.

The cookie file path can be configured using `--cookie-file`:

```
ord --cookie-file /path/to/cookie/file server
```

Alternatively, `ord` can be supplied with a username and password on the
command line:

```
ord --bitcoin-rpc-user foo --bitcoin-rpc-pass bar server
```

Using environment variables:

```
export ORD_BITCOIN_RPC_USER=foo
export ORD_BITCOIN_RPC_PASS=bar
ord server
```

Or in the config file:

```yaml
bitcoin_rpc_user: foo
bitcoin_rpc_pass: bar
```

Logging
--------

`ord` uses [env_logger](https://docs.rs/env_logger/latest/env_logger/). Set the
`RUST_LOG` environment variable in order to turn on logging. For example, run
the server and show `info`-level log messages and above:

```
$ RUST_LOG=info cargo run server
```

New Releases
------------

Release commit messages use the following template:

```
Release x.y.z

- Bump version: x.y.z → x.y.z
- Update changelog
- Update dependencies
- Update database schema version
```

Translations
------------

To translate [the docs](https://docs.ordinals.com) we use this
[mdBook i18n helper](https://github.com/google/mdbook-i18n-helpers).
So read through their [usage guide](https://github.com/google/mdbook-i18n-helpers/blob/main/USAGE.md)
to see the structure that translations should follow.

There are some other things to watch out for but feel free to just start a
translation and open a PR. A maintainer will then help you integrate it into our
build system.
