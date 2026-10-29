# gcslib/buildenv

A build environment for upstream build systems, and a rule that runs
`./configure && make && make install` as one hermetic step.

A GCSL library for pekit, Peios's build tool. Import it as
`github.com/gcslib/buildenv`.

## Usage

In `workspace.gcsl`, register its toolchains:

```text
import Toolchain::* from "github.com/gcslib/gcc"
import Toolchain::* from "github.com/gcslib/buildenv"
import Toolchain from toolchain

pekitconf = {toolchains = [Toolchain::C gcc_x86_64, Toolchain::BuildEnv busybox_x86_64]}
```

In a build file:

```text
import "github.com/gcslib/buildenv"
import unpack from archive

def build zlib() {
    let src = unpack(fetch(
        "https://zlib.net/fossils/zlib-1.3.1.tar.gz",
        sha256 = "9a93b2b7dfdac77ceba5a558a580e74667dd6fede4585b91eefb60f03b72df23",
    ), strip = 1)
    = buildenv.configure_make(src)
}
```

## What it provides

- `busybox_x86_64`: a pinned environment for x86-64 Linux, a static
  busybox and GNU make, so that a build reads nothing of the host.
- `host_tools`: the host's own tools instead.
- `configure_make(src, configure, make)`: what an upstream build installs,
  built with the registered C toolchain and environment. Every file of the
  source is given one time first, so `make` never tries to regenerate a
  file that a release ships.

It needs the [gcc](https://github.com/gcslib/gcc) library.

The comment at the top of `buildenv.gcsl` documents each in full.

## Status

gcslib is new, and its libraries' APIs can still change. Pin the commit you
build with: `pekit lock` does so in `pekit.lock`.

## Licence

MIT. See [LICENSE](LICENSE).
