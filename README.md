# vmod_rers (Regular Expression in RuSt)

This is a vmod for [varnish](http://varnish-cache.org/), allowing you to expand on what the native VCL regex can do.

Notably, it offers dynamic regex compilation backed by an LRU cache to speed processing up while limiting the memory footprint. On top of this, it supports named capturing groups which simplifies the handling of complex regex.

As usual, the full VCL API is described in [vmod.vcc](vmod.vcc), and if you need help, you can find me as `guillaume` on the [varnish discord server](https://discord.gg/EuwdvbZR6d).

## VCL Example

```bash
import rers;

sub vcl_init {
	new re_cache = rers.init(100);
}

sub vcl_recv {
	if (re_cache.is_match(req.url, "admin")) {
		return (pass);
	}
	if (re_cache.capture(req.http.authorization, "(\w+) (\S+)")) {
		set req.http.auth_type = re_cache.group(1);
		set req.http.auth_credential = re_cache.group(2);
	}
}
```

## Requirements

You'll need:
- `cargo` (and the accompanying `rust` package)
- `python3`
- the `varnish` development libraries/headers

## Version matching

Depending on which `varnish` version tou are targeting, select the right `vmod_rers` version (available as git tags, and as [releases](https://github.com/gquintard/vmod_rers/releases)).

| vmod-rers | varnish |
|:----------|:-------:|
| v0.0.14   |   9.0+  |
| v0.0.13   |   8.0+  |
| v0.0.12   |   7.7   |
| v0.0.10   |   7.6   |
| v0.0.9    |   7.5   |
| v0.0.8    |   7.4   |
| v0.0.7    |   7.3   |
| v0.0.6    |   7.3   |
| v0.0.5    |   7.2   |
| v0.0.4    |   7.0   |
| v0.0.3    |   7.0   |
| v0.0.2    |   7.0   |
| v0.0.1    |   7.0   |

## Build and test

With `cargo` only:

```bash
cargo build --release
cargo test --release
```

The vmod file will be found at `target/release/libvmod_rers.so`.

Alternatively, if you have `jq` and `rst2man`, you can use `build.sh`

```bash
./build.sh [OUTDIR]
```

This will place the `so` file as well as the generated documentation in the `OUT` directory (or in the current directory if `OUT` wasn't specified).
