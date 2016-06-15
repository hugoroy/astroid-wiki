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