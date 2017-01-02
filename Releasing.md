## Steps for making a new release

1. Make sure all stable PRs scheduled for the release are merged
1. scons
1. scons test
1. git tag -s vX.Y # include changes in comment
1. scons --release
1. scons --release test

# ready to publish
1. git push
1. git push --tags vX.Y

# announce
1. Send annoucement email to: astroidmail@googlegroups.com and notmuch@notmuchmail.org, including:
  * general description
  * main new features with link to full changelist
  * link to download page
1. Update Arch Linux package (possibly others..)