## Setup

1. First make sure you have private and public key pair, check out the [guide on Arch Linux](https://wiki.archlinux.org/index.php/GnuPG#Create_key_pair).
1. Publish your public key
1. Set `gpgkey` of the account in the astroid config to either the keyid or your email.
1. Get the key of the one you want to encrypt a message to (check out `gpg --search-keys`)
1. You're good.

> Note that you need a gpg-agent (recent gpg2 require this as well) with a *graphical pinentry*. On Arch Linux `pinentry-libsecret` in AUR allows you to save the password on the GNOME keyring.

### Auto trusting
You can set `crypto.gpg.always_trust` to `true` (default) to use keys for verification, even though they are not locally verified. Otherwise all keys must be locally verified: `gpg --lsign-key key`.

Setting `always_trust` to `true` will result in a successful verification for untrusted keys, but will show the level of trust in the message. If set to `false` the signature verification will fail for untrusted keys.

### Verification: auto-key-retrieve

You can set `crypto.gpg.auto_key_retrieve` to `false` to override a `keyserver-option auto-key-retrieve` in your `.gnupg/gpg.conf`. 

If you want to have auto-key-retrieve work, you need to enable it *both* in astroid (default: true) and in `gpg.conf`:
```
keyserver-options auto-key-retrieve
```

Note that this will add the key to your keyring (though it will not trust it).

### Important considerations
When encrypting to several recipients the keyids of the recipients will be included in the header information of the encrypted message. It is therefore possible to see who all of the intended receivers are. On the other hand, it is possible to fake these keyids so they should not be trusted on incoming emails - that is: you cannot be really sure that the alleged receiver of an encrypted message is really the receiver unless you have the secret key.

The keyids in the header packet are usually set to 0x0 when we want to hide the recipient. Currently astroid does not do this, but you can configure it in your gnupg config - make sure to test the result before you trust it. By default, therefore, astroid does not hide the _BCC_ receivers of a message. It is also not possible to hide the _number_ of receivers. So if you want to send an message to several receivers without giving any information about who, or the number, of recivers, you need to send it one by one manually.

### Troubleshooting
* Make sure you have the public key of the sender or the receiver.
* Make sure always_trust is set to true in `crypto.gpg.always_trust`: This means that astroid will use a key even though you have not marked it as valid.