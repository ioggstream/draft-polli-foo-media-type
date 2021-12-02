---
title: The "id-" prefix for Digest Algorithms
abbrev:
docname: draft-polli-id-digest-algorithms-latest
category: exp

ipr: trust200902
area: General
workgroup:
keyword: Internet-Draft

stand_alone: yes
pi: [toc, tocindent, sortrefs, symrefs, strict, compact, comments, inline, docmapping]

author:
 -
    ins: R. Polli
    name: Roberto Polli
    org: Digital Transformation Department, Italian Government
    email: robipolli@gmail.com
    country: Italy

normative:

informative:

--- abstract

This document defines
the "id-" prefix for digest-algorithms
used in the Digest Fields.
This prefix explicits that the computed checksum value
is independent from Content-Encoding.

--- note_Note_to_Readers

*RFC EDITOR: please remove this section before publication*

Discussion of this draft takes place on the HTTP working group mailing list
(ietf-http-wg@w3.org), which is archived at
<https://lists.w3.org/Archives/Public/ietf-http-wg/>.

The source code and issues list for this draft can be found at
<https://github.com/ioggstream/draft-polli-id-digest-algorithms>.


--- middle

# Introduction

The {{!DIGEST=I-D.ietf-httpbis-digest-headers}} defines a way to convey a checksum of a representation-data
as specified in {{!SEMANTICS=I-D.ietf-httpbis-semantics}}.

As the representation data depends on the value of `Content-Encoding`, it is useful
to convey the checksum value of a representation without any content coding applied.

This proposal introduces the `id-` prefix
to specify that the provided digest-algorithm value is computed on the representation-data
without any content coding applied.

## Notational Conventions
{::boilerplate bcp14+}

This document uses the Augmented BNF defined in {{!RFC5234}} and updated
by {{!RFC7405}}.

The definitions "representation", "selected representation", "representation
data", "representation metadata", and "content" in this document are to be
interpreted as described in {{SEMANTICS}}.

The definitions "digest-algorithm" and "representation-data-digest" in this document
are to be interpreted as described in {{DIGEST}}.


# The "id-" prefix for digest-algorithms {#id-prefix}

A new digest-algorithm to be registered within the
[HTTP Digest Algorithm Values Registry](https://www.iana.org/assignments/http-dig-alg/)
MUST NOT start with the string `id-`.

The following two examples show two digest-algorithm names that cannot be registered

~~~ example

   id-sha-256
   id-sha-512
~~~

For every digest-algorithm registered in the 
[HTTP Digest Algorithm Values](https://www.iana.org/assignments/http-dig-alg/)
the associate `id-` digest-algorithm has the following properties:

  * the checksum is computed on the representation-data of the resource
    when no content coding is applied;
  * the checksum is computed according to the original digest-algorithm
    "Description" field, and uses the same encoding of the original digest-algorithm.

# Security Considerations

## Disclosure of encrypted content

If the content coding provides encryption features,
sending the checksum of unencoded representation can
disclose information about the original content.

# IANA Considerations

Please, add the following text to the "Note" section
of the [HTTP Digest Algorithm Values](https://www.iana.org/assignments/http-dig-alg/).

"
For each registered Digest Algorithm, an associated `id-` algorithm is defined.

The associated representation-data-digest is computed according to {{id-prefix}}
of this document.
"

--- back

# Examples

## The id-sha-256 digest-algorithm

The following request conveys a brotli encoded
json object

~~~ example
{"hello": "world"}
~~~

The `Digest` computed using the "sha-256" digest-algorithm present in 
[HTTP Digest Algorithm Values](https://www.iana.org/assignments/http-dig-alg/)
is content coding aware,
while its associated "id-" digest-algorithm is not. 

~~~ example

POST /data HTTP/1.1
Content-Type: application/json
Content-Encoding: br
Digest: sha-256=4REjxQ4yrqUVicfSKYNO/cF9zNj5ANbzgDZt3/h3Qxo=,
        id-sha-256=X48E9qOokqqrvdts8nOJRJN3OWDUoyWxBf7kbu9DBPE=

CwGAZiwiAeyJoZWxsbyI6ICJ3b3JsZCJ9Aw==
~~~

# FAQ
{: numbered="false"}

_RFC Editor: Please remove this section before publication._

Q: Why to convey the checksum independent from the content codings?
:  This is useful to identify and validate a resource downloaded
   from different sources using different content codings, or
   to compare a resource with its stored or signed counterpart.

Q: How does it improve the life of checksum providers?
:  If providers use reverse proxies to eg. compress responses,
   this could invalidate content coding aware checksums.
   Providing an id- algorithm, allows the digest checksum to be
   validated.


# Code Samples
{:numbered="false"}

_RFC Editor: Please remove this section before publication._

How can I generate an identity digest value?

The following python3 code can be used to generate digests for json objects
using crc32c algorithm. Note that these are formatted as
base64. This function could be adapted to other algorithms and should take into
account their specific formatting rules.

~~~
import base64, json, brotli, hashlib

identity = lambda x: x

def digest(item, content_coding=identity, algorithm=hashlib.sha256) -> bytes:
    json_bytes = json.dumps(item).encode()
    content_encoded = content_coding(json_bytes)
    checksum = algorithm(content_encoded)
    return base64.encodebytes(checksum.digest())


item = {"hello": "world"}

print("sha-256 digest value for a br-coded representation: ",
    digest(item, content_coding=brotli.compress)
)

print("id-sha-256 digest value for a br-coded representation: ",
    digest(item, content_coding=identity)
)


~~~

# Acknowledgements
{: numbered="false"}

This specification was born from a thread created by James Manger
and the subsequent discussion here https://github.com/httpwg/http-extensions/issues/885.

# Change Log
{: numbered="false"}

_RFC Editor: Please remove this section before publication._

## Since draft-polli-id-digest-algorithms-01
{: numbered="false"}

* Include id-sha-256 and id-sha-512.
