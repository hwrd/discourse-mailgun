Mailgun plugin for Discourse
============================

Discourse supports replying and starting new topics via email only through
checking an external mailbox via POP3. This little plugin accepts incoming
email through an http endpoint.

This plugin only supports Mailgun, adapting this to work with other services
is trivial and it is left as an exercise to the reader.


Installation
------------

1. Make sure your Discourse instance works correctly, especially make sure
   it can deliver emails. It's not actually required for the plugin to work
   but it is required by Discourse anyway.
2. Follow [the
   istructions](https://meta.discourse.org/t/install-a-plugin/19157) to
   install a Discourse plugin. Don't forget to rebuild your instance.
3. In Settings > Email, tick "reply by email enabled" and set "reply by email
   address" to something like "replies+%{reply_key}@example.com" where
   example.com has MX records pointing to Mailgun.
4. In the admin panel go to Plugins > mailgun > Settings, tick "Mailgun plugin
   enabled" and copy the API key for "example.com" above in the "Mailgun API
   key" box.
5. At Mailgun, set up a route for "example.com" with a filter that matches the
   above address and with action
   `forward("http://your.discourse.instance/mailgun/mime")`. In principle your
   Discourse instance doesn't need to live on the same domain you use for
   incoming email. If you have TLS enable you can try to use https here, I
   don't know, I haven't tried yet.
6. Do a couple of tests and let me know if it works.

Troubleshooting
---------------

Keep an eye on Mailgun logs and on Discourse error log at `/logs` too.

Behaviour
---------

The plugin installs a route `/mailgun/mime` that accepts POST requests. The
controller:

1. Checks that the plugin is enabled.
2. Checks the request has a valid authenticity token.
3. Call the same procedure Discourse would use to process emails collected
   from the external mailbox.

In case 1. or 2. fail, the controller will reply with status 406 Not
Acceptable. This instructs Mailgun to not try to re-deliver the same message
again. If a message fails to process, the controller still replies with
200 OK, as, in the end, the message was successfully delivered to the
Discourse instance.

[Mailgun
documentation](https://documentation.mailgun.com/user_manual.html#receiving-forwarding-and-storing-messages)
for reference.

Contributing
------------

This is the first time I write something with Rails. If what you see is
hurting your eyes, please send a pull request!

