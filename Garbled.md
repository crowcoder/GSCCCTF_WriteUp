# Garbled

The clue is:

> 065051c69ad1c7fb518410ac79deb5ea

Now, this might look a lot like base64 encoding like you saw in the Welcome Challenge. However, you soon find out that it does not decode that way.

The biggest hint here is that the length is 32 characters. This is an md5 hash, chosen specifically because it is a known weak hash that you should not use an an encryption mechanism.

You will be able to find online crackers that can show you the original value of the hash. Not all of them can decode it, but keep trying until you find one that can.

