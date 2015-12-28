# Use Multiple Keys (Cards) in initramfs to decrypt luks

So gnupg is broken in initramfs. You can't use more than one Token (Smartcard / USBKey) in initram-fs Stage.
There are two problems:
* gnupg ignores every stub in the secring.gpg which isn't on position one
* gnupg doesn't try different card-readers (Slots / Ports), when there's more than 1 Reader present

## WARNING. This is a very dirty and quick Hack. it requires that you are firm with luks and gpg and already set up your luks-bootenvironment!

Let's solve this by a crappy workaround:
* Create a secring/publicring for EVERY Card you want to use and save them seperated in /etc/keys/[cardno]. Put only ONE stub in the File
* Encrypt your luks-secret with at least 3 targets.
	- Target 1: STRONG Symmetric Passphrase (always a good idea, because Hardware could fail). 
	- Target 2: 1st Token
	- Target 3: 2nd Token
* edit decrypt_gnupg_sc and cryptgnupg_sc and put the serials for your tokens to the right place at the end, where the stubs are copied to initramfs
* Throw both files to their destination:
	- cryptgnupg_sc to /etc/initramfs-tools/hooks/cryptgnupg_sc 
	- decrypt_gnupg_sc to /lib/cryptsetup/scripts/decrypt_gnupg_sc
