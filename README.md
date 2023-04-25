# pau

pau - perl application unit

This program ease the instalaltion of perl applications
(conventionally the ones under the namespace `App::`) to their own
isolated environment and expose just the application executable itself
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
    [ ! -e  ~/.local/bin/cpm ]
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

To remove the things we installed

    pau delete jt
    pau delete dzil

# what's the differences ?

The main function of `pau` is to install application X and all its
dependencies under an isolated and self-contained directory (a
`local::lib`). This is somewhat similar to building a container image
with just application X inside, except there is no virtual machines
involved.

This is maybe important because, if we are writing modules, the
authoring tool we are using might share some dependencies with the
module we are writing. If we wish to upgrade some dependencies for
the purpose of testing our work, we may unintentionally break the
tools, vice versa.

In a sense, pau is similar to PAR or fatpack but without the
put-everything-in-the-same-file part.

# why this approach

Comparing to have one shared land of site_perl for all our libs,
apps, tools and softwares, the approach of having isolated
installation for each one has some pros:

1. deletions becomes trivial and safe
2. upgrading one tool does not break others as a side-effect
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

In a more subtle way, when installing X and Y, they might declare that
they need different versions of Z. X might declare that it needs Z to
be at exactly version 1.0, while Y might declare that it needs Z at
least 2.0, which means X and Y cannot be installed together in the
same shared environment.

The cons of creating multilpe isolated installations approach is that
it requires more disk space and extra time when installing things,
since installations always start from scratch.
