gg
==

A helper for `git grep` and a hack upon a hack. Quicker to type and married to Vim.

What?
-----

To start with, this is an abbreviation for `git grep -E --heading -n`, which gives a nice output grouped by file and with line numbers. So far so good: this could just be a shell alias.

On top of that, it is a replacement for `grep` with much nicer output and less typing, because it adds the `--no-index` switch depending on whether it finds itself inside of a Git work tree or not. Fine, that needs more of a shell function.

It can also be invoked as `ggv` to start Vim with the files matched. OK, another alias.

Well, that is how I had `gg` and `ggv` written for a long time.

But here’s the trick: `ggv` will invoke Vim such that it knows what you searched for and will place you on the first match. Aliases and functions do not provide the reach necessary for that.

How?
----

Well, `git grep` has this very useful `-O` switch, you see, which tells it to invoke the named program with the list of files that matched the pattern you searched for. Even more nicely, `git grep` can also pass that pattern to that program invoked.

Except, this only works if the if you tell it to invoke `less` or `vi` – literally. That is, `git grep` will only pass the pattern along if the program name is one of those two exact strings. It does not do this by way of a convenient interface, you see: instead of, say, putting the value in an environment variable, it passes it as an argument to the command being invoked – which it only knows how to do for those two programs.

But so what if `git grep` doesn’t provide a convenient interface? Just do it the hard way:

1. As `ggv`, the script passes `-Ovi` to `git grep` – but only after creating a temporary directory, symlinking itself into it as `vi`, and putting that at the front of `PATH`. So when `git grep` tries to invoke vi, it actually invokes the same script again, just under another name. (This is why it cannot be an alias or function.) Now it can invoke Vim instead of vi.

2. Except, far more things are meta-characters in Vim very-magic regexp syntax than in GNU Extended syntax, yet in Vim normal regexp syntax, a few common things *aren’t*. So `ggv` yanks the pattern from the argument list and transliterates it from one syntax to the other. Yes, in pure bash. (It does not perform real translation, since the two regexp dialects have divergent support for various advanced constructs, and I am not about to write a transpiler here (and in pure bash, no less). So it just does a backslash switcharoo – good enough for everything I’ve ever thrown at it.)

As I said: a hack upon a hack.

Share and enjoy.
