# Alien::Base::Wrapper [![Build Status](https://secure.travis-ci.org/plicease/Alien-Base-Wrapper.png)](http://travis-ci.org/plicease/Alien-Base-Wrapper)

Compiler and linker wrapper for Alien

# SYNOPSIS

**NOTE**: this distribution has been ended.  [Test::Base::Wrapper](https://metacpan.org/pod/Test::Base::Wrapper) can now be found as part of the `Alien-Build` distribution.

From the command line:

    % perl -MAlien::Base::Wrapper=Alien::Foo,Alien::Bar -e cc -- -o foo.o -c foo.c
    % perl -MAlien::Base::Wrapper=Alien::Foo,Alien::Bar -e ld -- -o foo foo.o

From Makefile.PL (non-dynamic):

    use ExtUtils::MakeMaker;
    use Alien::Base::Wrapper qw( Alien::Foo Alien::Bar !export );
    
    WriteMakefile(
      'NAME'              => 'Foo::XS',
      'VERSION_FROM'      => 'lib/Foo/XS.pm',
      'CONFIGURE_REQUIRES => {
        'ExtUtils::MakeMaker' => 6.52,
        'Alien::Foo'          => 0,
        'Alien::Bar'          => 0,
      },
      Alien::Base::Wrapper->mm_args,
    );

From Makefile.PL (dynamic):

    use Devel::CheckLib qw( check_lib );
    use ExtUtils::MakeMaker 6.52;
    
    my @mm_args;
    my @libs;
    my %build_requires;
    
    if(check_lib( lib => [ 'foo' ] )
    {
      push @mm_args, LIBS => [ '-lfoo' ];
    }
    else
    {
      push @mm_args,
        CC => '$(FULLPERL) -MAlien::Base::Wrapper=Alien::Foo -e cc --',
        LD => '$(FULLPERL) -MAlien::Base::Wrapper=Alien::Foo -e ld --',
        BUILD_REQUIRES => {
          'Alien::Foo'           => 0,
          'Alien::Base::Wrapper' => 0,
        }
      ;
    }

    WriteMakefile(
      'NAME'         => 'Foo::XS',
      'VERSION_FROM' => 'lib/Foo/XS.pm',
      'CONFIGURE_REQUIRES => {
        'ExtUtils::MakeMaker' => 6.52,
      },
      @mm_args,
    );

# DESCRIPTION

This module acts as a wrapper around one or more [Alien](https://metacpan.org/pod/Alien) modules.  It is designed to work
with [Alien::Base](https://metacpan.org/pod/Alien::Base) based aliens, but it should work with any [Alien](https://metacpan.org/pod/Alien) which uses the same
essential API.

In the first example (from the command line), this class acts as a wrapper around the
compiler and linker that Perl is configured to use.  It takes the normal compiler and
linker flags and adds the flags provided by the Aliens specified, and then executes the
command.  It will print the command to the console so that you can see exactly what is
happening.

In the second example (from Makefile.PL non-dynamic), this class is used to generate the
appropriate [ExtUtils::MakeMaker](https://metacpan.org/pod/ExtUtils::MakeMaker) (EUMM) arguments needed to `WriteMakefile`.

In the third example (from Makefile.PL dynamic), we do a quick check to see if the simple
linker flag `-lfoo` will work, if so we use that.  If not, we use a wrapper around the
compiler and linker that will use the alien flags that are known at build time.  The
problem that this form attempts to solve is that compiler and linker flags typically
need to be determined at _configure_ time, when a distribution is installed, meaning
if you are going to use an [Alien](https://metacpan.org/pod/Alien) module then it needs to be a configure prerequisite,
even if the library is already installed and easily detected on the operating system.

The author of this module believes that the third (from Makefile.PL dynamic) form is
somewhat unnecessary.  [Alien](https://metacpan.org/pod/Alien) modules based on [Alien::Base](https://metacpan.org/pod/Alien::Base) have a few prerequisites,
but they are well maintained and reliable, so while there is a small cost in terms of extra
dependencies, the overall reliability thanks to reduced overall complexity.

# FUNCTIONS

## cc

    % perl -MAlien::Base::Wrapper=Alien::Foo -e cc -- cflags

Invoke the C compiler with the appropriate flags from `Alien::Foo` and what
is provided on the command line.

## ld

    % perl -MAlien::Base::Wrapper=Alien::Foo -e ld -- ldflags

Invoke the linker with the appropriate flags from `Alien::Foo` and what
is provided on the command line.

## mm\_args

    my %args = Alien::Base::Wrapper->mm_args;

Returns arguments that you can pass into `WriteMakefile` to compile/link against
the specified Aliens.

## mb\_args

    my %args = Alien::Base::Wrapper->mb_args;

Returns arguments that you can pass into the constructor to [Module::Build](https://metacpan.org/pod/Module::Build).

# ENVIRONMENT

Alien::Base::Wrapper responds to these environment variables:

- ALIEN\_BASE\_WRAPPER\_QUIET

    If set to true, do not print the command before executing

# SEE ALSO

[Alien::Base](https://metacpan.org/pod/Alien::Base), [Alien::Base](https://metacpan.org/pod/Alien::Base)

# AUTHOR

Graham Ollis <plicease@cpan.org>

# COPYRIGHT AND LICENSE

This software is copyright (c) 2017 by Graham Ollis.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
