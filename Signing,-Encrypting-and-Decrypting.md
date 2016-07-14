### Important considerations
When encrypting to several recipients the keyids of the recipients will be included in the header information of the encrypted message. It is therefore possible to see who all of the intended receivers are. On the other hand, it is possible to fake these keyids so they should not be trusted on incoming emails - that is: you cannot be really sure that the alleged receiver of an encrypted message is really the receiver unless you have the secret key.

The keyids in the header packet are usually set to 0x0 when we want to hide the recipient. Currently astroid does not do this, but you can configure it in your gnupg config - make sure to test the result before you trust it. By default, therefore, astroid does not hide the _BCC_ receivers of a message. It is also not possible to hide the _number_ of receivers. So if you want to send an message to several receivers without giving any information about who, or the number, of recivers, you need to send it one by one manually.

### Troubleshooting
* Make sure you have the public key of the sender or the receiver.
* Make sure always_trust is set to true in `crypto.gpg.always_trust`: This means that astroid will use a key even though you have not marked it as valid.