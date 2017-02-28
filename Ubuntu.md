# Ubuntu yakkety (16.10)
On yakkety you can install all the needed dependencies using this command:
```
$ sudo apt-get install git scons g++ libboost1.61-all-dev libnotmuch-dev libglibmm-2.4-dev libgtkmm-3.0-dev libwebkitgtk-3.0-dev libgmime-2.6-dev libsass-dev libpeas-dev libgirepository1.0-dev
```

Once done you can use
```
$ scons 
$ scons test
```

# Ubuntu xenial (16.04)
On xenial, some headers are either missing or different folders than expected (cf https://github.com/astroidmail/astroid/issues/211), hence you need to use this comand:
`$ CFLAGS="-I/usr/include/libpeas/ -I/usr/include/gobject-introspection-1.0" scons --disable-plugins`

To build and check that all the tests pass

# Ubuntu wily (15.10)
On a fresh Ubuntu 15.10 the following was required to get astroid going - but you should be able to get away with less.

The libsass-dev package is not available on Ubuntu 14.04 LTS due to [dependency issues](https://github.com/gauteh/astroid/issues/110). When building astroid on this release use the `--disable-sass` build option.

Install these packages:
```
git \
scons \
g++ \
libboost1.58-all-dev \
libnotmuch-dev \
libglibmm-2.4-dev \
libwebkit-dev \
libwebkitgtk-3.0 \
libwebkitgtk-3.0-dev \
libgtkmm-3.0-dev \
libgmime-2.6-dev \
libsass-dev
libpeas-dev
libgirepository1.0-dev
```

To build:
```
$ git clone https://github.com/gauteh/astroid.git # clone repository
$ cd astroid
$ scons # build
```

To set up `astroid` and see what other requirements are needed, check out the [[tour|Astroid-in-your-general-mail-setup]].

To run astroid:
```
$ ./astroid
```