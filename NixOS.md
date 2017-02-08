`astroid` can be installed directly using the nix package manager:
~~~
$ nix-env -f "<nixpkgs>" -iA astroid
~~~
Additionally, you will most likely want to install `notmuch` (the mail indexing backend for `astroid`), `offlineimap` (for receiving mail) and `msmtp` (for sending mail):
~~~
$ nix-env -f "<nixpkgs>" -iA notmuch offlineimap msmtp
~~~
To learn more about these dependencies and how to get started, check out the [tour](https://github.com/astroidmail/astroid/wiki/Astroid-in-your-general-mail-setup).