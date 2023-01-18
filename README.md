NAME
====

Time::Duration - rounded or exact English expression of durations

SYNOPSIS
========

Example use in a program that ends by noting its runtime:

    my $start_time = time;
    use Time::Duration;

    # then things that take all that time, and then ends:
    print "Runtime: ", duration(time - $start_time), ".\n";
    # see also duration_exact

Example use in a program that reports age of a file:

    use Time::Duration;
    my $file = 'that_file';
    my $age_in_seconds = SomeModule::get_modtime($file);
    print "$file was modified ", ago($age_in_seconds);
    # see also ago_exact

Example use with more concise output. Simply wrap the output of the other functions as a parameter to `concise`

    ago($age_in_seconds);                  # 23 hours and 56 minutes ago
    concise(ago($age_in_seconds));         # 23h56m ago
    concise(duration(time - $start_time)); # 4m8s

Example of future time

    from_now(120);            # 2 minutes from now
    concise(from_now(86178)); # 23h56m from now
    # see also from_now_exact
    later(86178);             # 23 hours and 56 minutes later
    concise(later(86178));    # 23h56m later

And things that happened earlier

    earlier(86178);          # 23 hours and 56 minutes earlier
    concise(earlier(86178)); # 23h56m earlier
    # see also earlier_exact

DESCRIPTION
===========

This module provides functions for expressing durations in rounded or exact terms.

In the first example in the Synopsis, using duration($interval_seconds):

  * If the `time - $start_time` is 3 seconds, this prints "Runtime: **3 seconds**.".

  * If it's 0 seconds, it's "Runtime: **0 seconds**.".

  * If it's 1 second, it's "Runtime: **1 second**.".

  * If it's 125 seconds, you get "Runtime: **2 minutes and 5 seconds**.".

  * If it's 3820 seconds (which is exactly 1h, 3m, 40s), you get it rounded to fit within two expressed units: "Runtime: **1 hour and 4 minutes**.".

  * Using duration_exact instead would return "Runtime: **1 hour, 3 minutes, and 40 seconds**".

In the second example in the Synopsis, using `ago($interval_seconds)`:

  * If the `$age_in_seconds` is 3 seconds, this prints "*file* was modified **3 seconds ago**".

  * If it's 0 seconds, it's "*file* was modified **just now**", as a special case.

  * If it's 1 second, it's "from **1 second ago**".

  * If it's 125 seconds, you get "*file* was modified **2 minutes and 5 seconds ago**".

  * If it's 3820 seconds (which is exactly 1h, 3m, 40s), you get it rounded to fit within two expressed units: "*file* was modified **1 hour and 4 minutes ago**".

  * Using `ago_exact` instead would return "*file* was modified **1 hour, 3 minutes, and 40 seconds ago**".

  * And if the file's modtime is, surprisingly, three seconds into the future, $age is -3, and you'll get the equally and appropriately surprising "*file* was modified **3 seconds from now**."

FUNCTIONS
=========

This module provides all the following functions, which are all exported by default when you call `use Time::Duration;`.

over
====



  * duration($seconds)

  * duration($seconds, $precision)

Returns English text expressing the approximate time duration of `abs($seconds)`, with at most `$precision || 2` expressed units. (That is, `duration($seconds)` is the same as `duration($seconds,2)`.)

For example, `duration(120)` or `duration(-120)` is "2 minutes". And `duration(0)` is "0 seconds".

The precision figure means that no more than that many units will be used in expressing the time duration. For example, 31,629,659 seconds is a duration of *exactly* 1 year, 1 day, 2 hours, and 59 seconds (assuming 1 year = exactly 365 days, as we do assume in this module). However, if you wanted an approximation of this to at most two expressed (i.e., nonzero) units, it would round it and truncate it to "1 year and 1 day". Max of 3 expressed units would get you "1 year, 1 day, and 2 hours". Max of 4 expressed units would get you "1 year, 1 day, 2 hours, and 59 seconds", which happens to be exactly true. Max of 5 (or more) expressed units would get you the same, since there are only four nonzero units possible in for that duration.

  * duration_exact($seconds)

Same as `duration($seconds)`, except that the returned value is an exact (unrounded) expression of `$seconds`. For example, `duration_exact(31629659)` returns "1 year, 1 day, 2 hours, and 59 seconds later", which is *exactly* true.

  * ago($seconds)

  * ago($seconds, $precision)

For a positive value of seconds, this prints the same as `duration($seconds, [$precision]) . S<' ago'>`. For example, ago(120) is "2 minutes ago". For a negative value of seconds, this prints the same as `duration($seconds, [$precision]) . S<' from now'>`. For example, ago(-120) is "2 minutes from now". As a special case, ago(0) returns "right now".

  * ago_exact($seconds)

Same as ago($seconds), except that the returned value is an exact (unrounded) expression of $seconds.

  * from_now($seconds)

  * from_now($seconds, $precision)

  * from_now_exact($seconds)

The same as `ago(-$seconds)`, `ago(-$seconds, $precision)`, `ago_exact(-$seconds)`. For example, `from_now(120)` is "2 minutes from now".

  * later($seconds)

  * later($seconds, $precision)

For a positive value of seconds, this prints the same as `duration($seconds, [$precision]) . S<' later'>`. For example, ago(120) is "2 minutes later". For a negative value of seconds, this prints the same as `duration($seconds, [$precision]) . S<' earlier'>`. For example, later(-120) is "2 minutes earlier". As a special case, later(0) returns "right then".

  * later_exact($seconds)

Same as later($seconds), except that the returned value is an exact (unrounded) expression of $seconds.

  * earlier($seconds)

  * earlier($seconds, $precision)

  * earlier_exact($seconds)

The same as later(-$seconds), later(-$seconds, $precision), later_exact(-$seconds). For example, earlier(120) is "2 minutes earlier".

  * concise( *function(* ... ) )

Concise takes the string output of one of the above functions and makes it more concise. For example, `ago(4567) ` returns "1 hour and 16 minutes ago", but `concise(ago(4567)) ` returns "1h16m ago".

back
====



I18N/L10N NOTES
===============

Little of the internals of this module are English-specific. See source and/or contact me if you're interested in making a localized version for some other language than English.

BACKSTORY
=========

Sean Burke wrote the basic `ago()` function for use in Infobot (`http://www.infobot.org`), because he was tired of this sort of response from the Purl Infobot:

    me> Purl, seen Woozle?
    <Purl> Woozle was last seen on #perl 20 days, 7 hours, 32 minutes
    and 40 seconds ago, saying: Wuzzle!

He figured if it was 20 days ago, he don't care about the seconds. So once he had written `ago()`, he abstracted the code a bit and got all the other functions.

CAVEAT
======

This module calls a durational "year" an interval of exactly 365 days of exactly 24 hours each, with no provision for leap years or monkey business with 23/25 hour days (much less leap seconds!). But since the main work of this module is approximation, that shouldn't be a great problem for most purposes.

SEE ALSO
========

[Date::Interval](Date::Interval), which is similarly named, but does something rather different.

*Star Trek: The Next Generation* (1987-1994), where the character Data would express time durations like "1 year, 20 days, 22 hours, 59 minutes, and 35 seconds" instead of rounding to "1 year and 21 days". This is because no-one ever told him to `use Time::Duration`.

COPYRIGHT AND DISCLAIMER
========================

Copyright 2006, Sean M. Burke, Copyright 2013, Dagur Valberg Johannsson, Copyright 2023, Kay Rhodes

all rights reserved. This program is free software; you can redistribute it and/or modify it under the same terms as Perl 5 itself.

This program is distributed in the hope that it will be useful, but without any warranty; without even the implied warranty of merchantability or fitness for a particular purpose.

AUTHOR
======

  * Original Author: Sean M. Burke

  * Perl 5 Maintainer: Avi Finkel

  * Ported to Perl 6 by: Dagur Valberg Johannsson

  * Maintained in Perl 6 by: masukomi (A.K.A. Kay Rhodes)

