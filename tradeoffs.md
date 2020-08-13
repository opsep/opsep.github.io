---
title: Tradeoffs
toc: true
pre_toc: |
  We believe the OpSep protocol is <strong>strictly superior</strong> to how 99&#37; of companies secure their data, but there are limitations:


...


#### For Bulk Data Only
While our security model is <em>additive</em>, we only provide additional protection for storing secrets if you <em>want</em> your rate-limiting policy to block bulk decryption of all the records.
For example, if you were storing a handful of environmental variables and needed to be able to decrypt them all at once to spin-up a new server, then we provide minimal additional protection.
You would get the benefit of having OpSep log of all decryption operations that take place, but there are many ways to accomplish this goal.

OpSep is ideal for large datasets where your regular access pattern is to decrypt only a small fraction of that data each day.
Useres like OpSep for protecting scans of documents, account/routing number, medical records, credit card #s, SSNs (and other sensitive government records), private messages and more.

#### Not 100% Secure
Of course, nothing is.
With a normal rate-limit policy, OpSep ensures that an attacker with complete access (root/physical access to your server, the ciphertext in your database, <em>and</em> your API token) can only attempt to decrypt a tiny amount of data.
You can also setup montioring/notifications of suspicious activity.
The rate-limit policy is something you set based on your access pattern for decrypting data; encryption is non-interactive and not rate-limited.
Most users set their rate-limits to risk at most 0.1&#37; of their sensitive records, compared to a typical smash-and-grab attack of 100&#37; of encrypted records.

OpSep's security model is <em>additive</em> and compatible with other cryptosystems.
For example, some users encrypt their OpSep ciphertext with other cryptographic tools as well.


#### Reliance on OpSep Uptime for Decryption Operations
Our trustless operation means an OpSep server can't access your plaintext or ciphertext, even if it were compromised.
This is fundamentally different than most services that promise that they won't peak (or get hacked).
However, if your OpSep server were to go offline you couldn't decrypt your data until it was back online.

Keep in mind that both symmetric and asymmetric encryption take place locally in your datacenter, so this would have no effect on your ability to encrypt (save) new records during any downtime.
OpSep is popular with users whose access patterns mean they interact with sensitive data when it's being created and then rarely decrypt it.
For example, you might use PII on account creation to check a new user against a vendor API, and the only time you later decrypt that PII would for a background process that isn't time-sensitive.

Of course, since you control your keys, you can easily spin up another OpSep server (or put your RSA key into the client library and skip the OpSep server rate-limits altogether).
Your security model would now be downgraded to look like most sites that use a single key to protect all their data.

#### Searching on Encrypted Values Isn't Possible
Like most cryptosystems, we face a tradeoff between searchability and security, and OpSep is built around security-first.
Let's say you were protecting a social security number with OpSep.
You could trivially query your database to "get the social security number (in plaintext) of user ID X", but you couldn't query your database to "get the user whose social security number is Y."
This is fine for many use-cases if you have other ways to lookup a user (user ID, email, handle, phone number, address, etc) and then query to decrypt their sensitive data.

For some use-cases like encrypting a whole document/image, you rarely want to query by that anyway.
You could choose to store the hash digest of that file in plaintext and query on that if you had the document and wanted to search for what it was associated with.

Keep in mind for all these examples that OpSep has no idea what type of data you're securing, the ciphertext the OpSep server see sis always just 512 bytes of DEK (and related) material.

#### Complexity is the Enemy of Security
This maxim of cryptography applies to us as well.
Thankfully, our hybrid encryption scheme relies on battle-tested cryptography and is completely open-source and verifiable.

Our client libraries massively reduce complexity for developers who want to implement encryption for sensitive data.
If you don't like any of the choices we've made in our client library, you can tweak them however you like as symmetric encryption/decryption takes place locally.
