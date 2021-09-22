# pau

pau - perl application unit

This program ease the instalaltion of perl applications
(conventionally the ones under the namespace `App::`) to their own
isolated environment and expose just the application itself to
outside. (`~/.local/bin`)

# requirements

- bash
- perl (either system or perlbrew)
- cpm
- `~/.local/bin` in $PATH

# installation

Save `pau` under `~/.local/bin`

# usage examples

To install `ccdiff` from `App::ccdiff` and `jt` from `App::jt`

    pau install ccdiff
    pau install jt

To install `mbtiny` from `App::ModuleBuildTiny` and `dzil` from `Dist::Zilla`

    pau install -M App::ModuleBuildTiny mbtiny
    pau install -M Dist::Zilla jt

To remove the things we installed

    pau delete jt

# what's the differences ?

The main function of `pau` is to install application X and all its
dependencies under its own directory (a `local::lib`). This is
somewhat similar to building a container image with just application X
inside, except there is no virtual machines involved.

This is maybe important because, if we are writing modules, the
authoring tool we are using might share some dependencies with the
module you are writing. If we wish to upgrade some dependiencs for
the purpose of testing your work, we may unintentionally break your
tools, vice versa.

In a sense, this is similar to PAR or fatpack, just without the
put-everything-in-the-same-file part.

