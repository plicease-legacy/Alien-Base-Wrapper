# Alien::Base::Wrapper [![Build Status](https://secure.travis-ci.org/plicease/Alien-Base-Wrapper.png)](http://travis-ci.org/plicease/Alien-Base-Wrapper)

Compiler and linker wrapper for late optional Alien utilization

# SYNOPSIS

    % perl -MAlien::Base::Wrapper=Alien::Foo,Alien::Bar -e cc -o foo.o -c foo.c
    % perl -MAlien::Base::Wrapper=Alien::Foo,Alien::Bar -e ld -o foo foo.o

# DESCRIPTION

**Note**: this particular module is still somewhat experimental.

This module provides a command line wrapper for [Alien](https://metacpan.org/pod/Alien) modules that are
based on [Alien::Base](https://metacpan.org/pod/Alien::Base).  The idea is to eventually use this to allow optional
use of [Alien](https://metacpan.org/pod/Alien) modules by XS which cannot probe for a system library.
Historically an XS module that wanted to use an [Alien](https://metacpan.org/pod/Alien) had to _always_ have
it as a prerequisite.

# FUNCTIONS

## cc

    % perl -MAlien::Base::Wrapper=Alien::Foo -e cc cflags

Invoke the C compiler with the appropriate flags from `Alien::Foo` and what
is provided on the command line.

## ld

    % perl -MAlien::Base::Wrapper=Alien::Foo -e ld ldflags

Invoke the linker with the appropriate flags from `Alien::Foo` and what
is provided on the command line.

# ENVIRONMENT

Alien::Base::Wrapper responds to these environment variables:

- ALIEN\_BASE\_WRAPPER\_QUIET

    Do not print the command before executing

# SEE ALSO

[Alien::Base](https://metacpan.org/pod/Alien::Base), [Alien::Base](https://metacpan.org/pod/Alien::Base)

# AUTHOR

Graham Ollis <plicease@cpan.org>

# COPYRIGHT AND LICENSE

This software is copyright (c) 2017 by Graham Ollis.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
