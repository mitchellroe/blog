# bash expansion

## History

From <https://unix.stackexchange.com/questions/194406/how-to-access-the-second-argument-from-the-last-command-in-the-history>:

> You can use `<M-.>` (or `<Esc>`. if your Meta key is being used for something
> else), that is, Meta-dot (or Esc-dot), where Meta is usually the Alt key, to
> recall the last argument of the previous command. So, first you would type
>
> ```sh
> grep foo /usr/share/dict/american-english
> ```
>
> And then if you wanted to grep for something else, you would type
>
> ```sh
> grep bar
> ```
>
> After typing a space and then Esc. (that is, first pressing the escape key,
> and then the period key):
>
> ```sh
> grep bar /usr/share/dict/american-english
> ```
>
> You can also use either of the following:
>
> ```sh
> grep bar !:2
> grep bar !$
> ```
>
> Where `!:2` and `!$` mean "second argument" and "last argument" respectively.
