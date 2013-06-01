swift-digisign
==============

An OpenStack/Swift middleware for digital signature calculation and verification of stored objects.

This is a "proof-of-concept" implementation for server-side digital signature framework.
The current implementation signs stored objects during PUT request handling,
using prebuilt (hard-coded) sets of DSA key pairs that are stored on the same server,
and performs signature verification during GET request processing.
A requested object with a valid signature is returned to the client along with the signature metadata
(`X-Object-Meta-Sig-User` contains the signing user name, `X-Object-Meta-DSA-Signature` contains a DSA signature tuple),
in order to allow the client to verify the signature herself (see example in the demo file in the test directory).

Presentation: http://prezi.com/lr1mji53inxv/extending-swift-with-digital-signature-middleware/

Developed and presented as part of the Tel-Aviv University course "Advanced Topics in Storage Systems"
(http://www.eng.tau.ac.il/semcom/), spring 2013.

Installation & Configuration
----------------------------
- Install the module on the proxy server node (using `python setup.py install`)
- Add "digisign" to the proxy server pipeline (after auth module!)
- Add a configuration section to the bottom of the proxy server configuration file:

        [filter:digisign]
        use = egg:digisign#digisign
        keys_file = /path/to/prebuilt-key-file.dat

The prebuilt key-file is a pickled Python dictionary, with an element for every user in the system,
where the element key is the username and the element value is a DSA key object generated by PyCrypto
(see `keygen.py` in the test directory for an example of key-file generation).

This module uses [PyCrypto](https://www.dlitz.net/software/pycrypto/) and [WebOb](http://webob.org/).

Warnings & Disclaimers
----------------------
- This is *just* a POC submitted as an academic project - far from ready for use in production!
- Specifically, all cryptography and key handling happens on the same proxy server node
  (and not a dedicated HSM or something of this sort), which makes it inadequate in face of malicious administrators...
- There is **NO** "real" key management - keys are static and configured manually...
- Certificates are not used. A malicious user can resign objects and change the metadata to contain her public key!
- The implementation contains a "backdoor" (disabled by default) for demo purposes.
