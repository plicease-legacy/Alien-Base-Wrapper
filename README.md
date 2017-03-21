# Alien::Base::Wrapper [![Build Status](https://secure.travis-ci.org/plicease/Alien-Base-Wrapper.png)](http://travis-ci.org/plicease/Alien-Base-Wrapper)

Compiler and linker wrapper for late optional Alien utilization

# SYNOPSIS

From the command line:

    % perl -MAlien::Base::Wrapper=Alien::Foo,Alien::Bar -e cc -- -o foo.o -c foo.c
    % perl -MAlien::Base::Wrapper=Alien::Foo,Alien::Bar -e ld -- -o foo foo.o

From Makefile.PL:

    use Config;
    use ExtUtils::MakeMaker 6.52;
    
    my $cc      = $Config{cc};
    my $ld      = $Config{ld};
    my $libs    = '';
    my $ccflags = $Config{ccflags};
    my %build_requires;
    
    system 'pkg-config', '--exists', 'libfoo';
    if($? == 0)
    {
      $ccflags = `pkg-config --cflags libfoo` . " $ccflags";
      $libs    = `pkg-config --libs   libfoo`;
      delete $build_requires{'Alien::Libfoo'};
      delete $build_requires{'Alien::Base::Wrapper'};
    }
    else
    {
      $cc = '$(FULLPERL) -Iinc -MAlien::Base::Wrapper=Alien::Libfoo -e cc --';
      $ld = '$(FULLPERL) -Iinc -MAlien::Base::Wrapper=Alien::Libfoo -e ld --';
      $build_requires{'Alien::Libfoo'} = 0;
      $build_requires{'Alien::Base::Wrapper'} = 0;
    }
    
    WriteMakefile(
      NAME => 'Foo::XS',
      BUILD_REQUIRES => \%build_requires,
      CC             => $cc,
      LD             => $ld,
      CCFLAGS        => $ccflags,
      LIBS           => [ $libs ],
      ...
    );

# DESCRIPTION

**Note**: this particular module is still somewhat experimental.

This module provides a command line wrapper for [Alien](https://metacpan.org/pod/Alien) modules that are
based on [Alien::Base](https://metacpan.org/pod/Alien::Base).  The idea is to eventually use this to allow optional
use of [Alien](https://metacpan.org/pod/Alien) modules by XS which cannot probe for a system library.
Historically an XS module that wanted to use an [Alien](https://metacpan.org/pod/Alien) had to _always_ have
it as a prerequisite.

For a working example, please see the `Makefile.PL` that comes with [Term::EditLine](https://metacpan.org/pod/Term::EditLine).

For a more custom, non [Alien::Base](https://metacpan.org/pod/Alien::Base) based example, see the `Makefile.PL` that
comes with [PkgConfig::LibPkgConf](https://metacpan.org/pod/PkgConfig::LibPkgConf).

# FUNCTIONS

## cc

    % perl -MAlien::Base::Wrapper=Alien::Foo -e cc -- cflags

Invoke the C compiler with the appropriate flags from `Alien::Foo` and what
is provided on the command line.

## ld

    % perl -MAlien::Base::Wrapper=Alien::Foo -e ld -- ldflags

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
