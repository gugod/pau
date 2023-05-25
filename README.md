# pau

pau - perl application unit

This program prepare isolated enviroments ("units") for installing
perl applications -- the one that are conventionally named under the
namespace `App::` -- and expose the application executable itself
under `~/.local/bin`.

# requirements

- bash
- perl (either system or perlbrew)
- [cpm](https://github.com/skaji/cpm)
- `~/.local/bin` in `$PATH`

# installation

Here's a quick installer:

    curl -fsSL https://github.com/gugod/pau/raw/fun/pau > ~/.local/bin/pau \
        && chmod +x ~/.local/bin/pau
    [ ! -e  ~/.local/bin/cpm ] \
        && curl -fsSL https://git.io/cpm > ~/.local/bin/cpm \
        && chmod +x  ~/.local/bin/cpm

Then, ensure that `~/.local/bin` is in `$PATH`.

The second command of the installer is installing `cpm` to
`~/.local/bin`. This command may be skipped if there is already a
`cpm` in `$PATH`.

For `perl`, as long as there is one in `$PATH` and discoverable by
doing `which perl`, it'll be ok. That would be the one used to run the
apps installed via `pau`.

# usage examples

To install `ccdiff` from `App::ccdiff` and `jt` from `App::jt`

    pau install ccdiff
    pau install jt

To install `mbtiny` from `App::ModuleBuildTiny`

    pau install -M App::ModuleBuildTiny mbtiny

To install `dzil` from `Dist::Zilla` with `Dist::Zilla::Plugin::Git`
plugin "bundled" together:

    pau install -M Dist::Zilla -M Dist::Zilla::Plugin::Git dzil

To see what we have installed so far

    pau list

To remove the things we installed

    pau delete jt
    pau delete dzil

# what's the key feature of pau ?

The main function of `pau` is to install application X and all its
dependencies inside a dedicated "unit" -- an isolated and
self-contained directory, a `local::lib`.

It is somewhat similar to building a container image with just
application X inside, except there is no virtual machines or container
involved.

pau is similar to [PAR::Packer][] or [fatpack][] but without the
squeeze-everything-in-the-same-file part.

# why this approach

Comparing to have one shared land of site_perl for all our libs,
apps, tools and softwares, the approach of having isolated
installation for each one has some pros:

1. deletions becomes trivial and safe
2. upgrading one tool can never break other tools by accident.
3. contention of dependencies can never occur in between apps

Deleting stuff inside site_perl has always been a bit tricky. There
are tools for deleting individual modules X but its dependencies may
not be recursively deleted. This is mostly due to lack of
implementation but nonetheless it is what we have now.

Even if there are tools to removes X and all its dependencies, doing
so may break Y, because X and Y share a dependency Z which has to be
kept in order to keep Y working.

If all installations are isolated, there would be multiple copies of
Z. Deleting X+Z would not effect Y+Z.

There is also an opposite of the same problem when it comes to
upgrading things. When we are upgrading X, Z might need to be upgraded
together and that might break Y.

Y might be broken in a more subtle way: when X and Y requires
different versions of Z to work. X might declare that it needs Z to be
at exactly version 1.0, while Y might declare that it needs Z at least
2.0, which means X and Y cannot be installed together in the same
shared environment.

Install X and Y in their own isolated enviroment avoid all such
problems of dependency contention.

The cons of creating multilpe isolated installations approach is that
it requires more disk space and extra time when installing things,
since installations always start from scratch.

# See also

- [PAR::Packer][] and [PAR][]
- [fatpack][]
- [pipx][]

[PAR]: https://metacpan.org/pod/PAR
[PAR::Packer]: https://metacpan.org/pod/PAR::Packer
[fatpack]: https://metacpan.org/dist/App-FatPacker/view/bin/fatpack
[pipx]: https://pypa.github.io/pipx/
