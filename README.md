## Introducing Hegemon

[![Crates.io](https://img.shields.io/crates/v/hegemon.svg)](https://crates.io/crates/hegemon)
[![Build Status](https://travis-ci.org/p-e-w/hegemon.svg?branch=master)](https://travis-ci.org/p-e-w/hegemon)

Hegemon is a **work-in-progress** [modular](#adding-new-data-streams) system monitor
written in safe Rust.

![Screencast](https://user-images.githubusercontent.com/2702526/45913619-818f1100-be53-11e8-8138-6ddfe42e15af.gif)

Currently, it has the following features:

- Monitor CPU and memory usage, temperatures, and fan speeds
- Expand any data stream to reveal a more detailed graph and additional information
- Adjustable update interval
- Clean MVC architecture with good code quality
- Unit tests

**Planned** features include:

- macOS and BSD support (only Linux is supported at the moment)
- Monitor disk and network I/O, GPU usage (maybe), and more
- Select and reorder data streams
- Mouse control

Hegemon is built around the excellent [chan](https://github.com/BurntSushi/chan),
[termion](https://gitlab.redox-os.org/redox-os/termion),
[systemstat](https://github.com/myfreeweb/systemstat),
and [sensors](https://github.com/nyantec/sensors) crates.


## Installation

Hegemon is currently **Linux only** and requires **Rust 1.26 or later** and the
**development files for [libsensors](https://github.com/lm-sensors/lm-sensors).**
The latter can be found in the package repository of every major Linux distribution,
e.g. `lm_sensors-devel` in Fedora and `libsensors4-dev` in Ubuntu.

Once these requirements are met, you can install Hegemon with

```
$ cargo install hegemon
```


## Adding new data streams

Unlike traditional system monitors such as `top`, which are tailor-made
to display specific metrics like CPU and memory usage only, Hegemon shows
the output of monitoring modules called *data streams,* whose behavior is
defined by the [`Stream`](src/stream.rs) trait.

Streams only need to supply basic properties such as name and description,
and a method for retrieving a numeric data value. Everything else is managed
by Hegemon, including update scheduling, layout and rendering, adaptive scaling,
and computation of statistics.

`Stream` objects are in turn generated by [`StreamProvider`](src/stream.rs)s.
This makes it possible to have streams whose exact nature and number can only
be determined at runtime, such as one stream per CPU core.

Adding new streams, then, involves the following steps:

1. Create a `StreamProvider`
2. Make it return `Stream` objects from its `streams` method
3. Register the provider in [`providers/mod.rs`](src/providers/mod.rs)

The [`providers`](src/providers) directory contains several working provider
implementations that can be used as a reference. In particular, most providers
will want to use the `Stream::new` helper function to create streams
instead of manually implementing the `Stream` trait.

**Ideas for, and implementations of, additional data streams are very welcome!**


## License

Copyright &copy; 2018  Philipp Emanuel Weidmann (<pew@worldwidemann.com>)

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <https://www.gnu.org/licenses/>.

**By contributing to this project, you agree to release your
contributions under the same license.**
