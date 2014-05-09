# NAME

Catalyst::Controller::ActionRole - Apply roles to action instances (DEPRECATED)

# VERSION

version 0.16

# SYNOPSIS

    package MyApp::Controller::Foo;

    use Moose;
    use namespace::autoclean;

    BEGIN { extends 'Catalyst::Controller::ActionRole' }

    sub bar : Local Does('Moo') { ... }

# DESCRIPTION

This module allows to apply [Moose::Role](https://metacpan.org/pod/Moose::Role)s to the `Catalyst::Action`s for
different controller methods.

For that a `Does` attribute is provided. That attribute takes an argument,
that determines the role, which is going to be applied. If that argument is
prefixed with `+`, it is assumed to be the full name of the role. If it's
prefixed with `~`, the name of your application followed by
`::ActionRole::` is prepended. If it isn't prefixed with `+` or `~`,
the role name will be searched for in `@INC` according to the rules for
[role prefix searching](#role-prefix-searching).

It's possible to apply roles to __all__ actions of a controller without
specifying the `Does` keyword in every action definition:

    package MyApp::Controller::Bar

    use Moose;
    use namespace::autoclean;

    BEGIN { extends 'Catalyst::Controller::ActionRole' }

    __PACKAGE__->config(
        action_roles => ['Foo', '~Bar'],
    );

    # Has Catalyst::ActionRole::Foo and MyApp::ActionRole::Bar applied.
    #
    # If MyApp::ActionRole::Foo exists and is loadable, it will take
    # precedence over Catalyst::ActionRole::Foo.
    #
    # If MyApp::ActionRole::Bar exists and is loadable, it will be loaded,
    # but even if it doesn't exist Catalyst::ActionRole::Bar will not be loaded.
    sub moo : Local { ... }

Additionally, roles can be applied to selected actions without specifying
`Does` using ["action" in Catalyst::Controller](https://metacpan.org/pod/Catalyst::Controller#action) and configured with
["action\_args" in Catalyst::Controller](https://metacpan.org/pod/Catalyst::Controller#action_args):

    package MyApp::Controller::Baz;

    use Moose;
    use namespace::autoclean;

    BEGIN { extends 'Catalyst::Controller::ActionRole' }

    __PACKAGE__->config(
        action_roles => [qw( Foo )],
        action       => {
            some_action    => { Does => [qw( ~Bar )] },
            another_action => { Does => [qw( +MyActionRole::Baz )] },
        },
        action_args  => {
            another_action => { customarg => 'arg1' },
        }
    );

    # has Catalyst::ActionRole::Foo and MyApp::ActionRole::Bar applied
    sub some_action : Local { ... }

    # has Catalyst::ActionRole::Foo and MyActionRole::Baz applied
    # and associated action class would get additional arguments passed
    sub another_action : Local { ... }

# ATTRIBUTES

## \_action\_role\_prefix

This class attribute stores an array reference of role prefixes to search for
role names in if they aren't prefixed with `+` or `~`. It defaults to
`[ 'Catalyst::ActionRole::' ]`.  See ["role prefix searching"](#role-prefix-searching).

## \_action\_roles

This attribute stores an array reference of role names that will be applied to
every action of this controller. It can be set by passing a `action_roles`
argument to the constructor. The same expansions as for `Does` will be
performed.

# METHODS

## gather\_action\_roles(\\%action\_args)

Gathers the list of roles to apply to an action with the given `%action_args`.

# ROLE PREFIX SEARCHING

Roles specified with no prefix are looked up under a set of role prefixes.  The
first prefix is always `MyApp::ActionRole::` (with `MyApp` replaced as
appropriate for your application); the following prefixes are taken from the
`_action_role_prefix` attribute.

# DEPRECATION NOTICE

As of version `5.90013`, [Catalyst](https://metacpan.org/pod/Catalyst) has merged this functionality into the
core [Catalyst::Controller](https://metacpan.org/pod/Catalyst::Controller).  You should no longer use it for new development
and we'd recommend switching to the core controller as soon as practical.

# AUTHOR

Florian Ragwitz <rafl@debian.org>

# CONTRIBUTORS

- Alex J. G. Burzyński <ajgb@ajgb.net>
- Hans Dieter Pearcey <hdp@weftsoar.net>
- Jason Kohles <email@jasonkohles.com>
- John Napiorkowski <jjnapiork@cpan.org>
- Karen Etheridge <ether@cpan.org>
- NAKAGAWA Masaki <masaki.nakagawa@gmail.com>
- Tomas Doran <bobtfish@bobtfish.net>
- William King <william.king@quentustech.com>

# COPYRIGHT AND LICENSE

This software is copyright (c) 2009 by Florian Ragwitz.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
