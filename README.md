# SpamAssassin anti-spambot rules

## Introduction

If your credit card and personal information is stolen, fraudsters
will sometimes use spambot to subscribe your email address to hundreds
of mailing lists, register at online stores, and so on.  This is done
in an attempt to hide fraudlent banking notifications in a sea of
other difficult-to-filter noise.

This happened to me recently, but the fraudulent transaction was
easy to spot because it triggered text messages and emails from my
credit card company and the fradulent signups started a few minutes
later.   Unfortunately, because the signups are using your email address
and a random name/address/password for legitimate mailing lists, online
stores, etc, the email is difficult to filter.   Fortunately, the spambot
behavior only lasts for a few days (enough to allegedly hide the fradulent
traffic), but you may encounter hundreds of signups during that time.

These messages come from websites that automatically generate emails by
entering your email address and, potentially, some unvalidated personal
information, but do not have even simple captcha-type verification configured.
Popular storefronts, such as Shopify-based stores fall into this category,
as do many WordPress sites and legitimate mailing lists which send an
address verification email.

## One solution

This set of SpamAssassin rules captures most of these fradulent messages.
The false-positive rate is not insignficant, but if you are expecting
specific emails or do spend some time sorting through the filtered messages
these rules will help keep your main inbox clear.  These rules should be useful
for the limited time during which the spambots are actively registering your
email address for unwanted messages, but they are too broad for general use.

The rules have some broad-spectrum filters which may need to be adjusted for
your locale.  For example, I am US-based and speak english but interact with
many people from the UK, so anything not from the US or UK or is not in
English is immediately suspect for me.  Emails that look like a new account,
mailing list signup, or the like is filtered.


## Configuration/Installation

It is recommended that these custom SpamAssassin rules are used to filter
email to a separate mail folder, but not used for unconditional deletion.
For mailing lists that don't ask for affirmative verification of your email
address, it will be useful to have the original emails because the unsubscribe
links included in them generally do work.

Please look through the rules before installing.  Comment out or modify any
rules that may be specific to your environment.

### Requirements

 - A functioning SpamAssassin, version 3.4.0 or later
 - Mail filtering tool (e.g., procmail or sieve)


### To install:

  - Copy the rules file to your SpamAssassin installation directory

    On Debian:
    ```
    cp rules/spambot.cf /etc/spamassassin/
    ```
     
  - Reload the spamassassin service

```
$ sudo systemctl restart spamassassin
```

Add procmail or sieve rules to your mail filter to match on the new rules
and file marked emails in their own folder.    For procmail, this will
file the marked emails in a folder called Spambot:

```
# As these are fairly broad filters, run them last
:0:
* ^X-Spam-Status:.*SPAMBOT_.*_
Spambot
```
It is suggested that these rules be evaluated after other matching rules.

The rules can be disabled once an attack is finished by removing the
```spambot.cf``` file and restarting spamassassin.
