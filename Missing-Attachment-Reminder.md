You can configure astroid to remind you of missing attachments if certain keywords are present in a message.

### Defining keywords

The keywords astroid searches for can be defined in the normal config file with the `editor.attachment_words` value. By default, its value is `attach`. This gives a warning before sending a message that includes this string and doesn't have something attached. Using the value `attach`, you can also catch words like "attached", "attachment" or "attaching". The value is case-insensitive.

To define other words, you can add further strings separated by commas. For instance, depending on the language, users can use these settings:


#### French (fr)

```
    "editor": {
        "attachment_words": "attach,pj,pièce jointe, pièce-jointe, pièces-jointes,ci-joint",
        [...]
    },
```

#### German (de)

```
    "editor": {
        "attachment_words": "attach,anbei,anhang,angehängt",
        [...]
    },
```

### Caveats

- Please note that using _anbei_ as `attachment_words` would also warn you if you used the word _anbeißen_ in a message.
- Note that the attachment words are also considered in the whole message's body. This also causes warnings if a trigger word appeared in a quoted part of the message.