---
title: FOO Media Type
abbrev:
docname: draft-polli-foo-media-type-latest
category: info

ipr: trust200902
area: General
workgroup:
keyword: Internet-Draft

stand_alone: yes
pi: [toc, tocindent, sortrefs, symrefs, strict, compact, comments, inline, docmapping]

venue:
  home: https://github.com/ioggstream/draft-polli-foo-media-type
  repo: https://github.com/ioggstream/draft-polli-foo-media-type/issues
# mail: robipolli@gmail.com

# github-issue-label: editorial

author:
 -
    ins: R. Polli
    name: Roberto Polli
    org: Digital Transformation Department, Italian Government
    email: robipolli@gmail.com
    country: Italy

normative:
  YAML:
    title: YAML Ain't Markup Language Version 1.2
    date: 2021-10-01
    author:
    - ins: Oren Ben-Kiki
    - ins: Clark Evans
    - ins: Ingy dot Net
    - ins: Tina Müller
    - ins: Pantelis Antoniou
    - ins: Eemeli Aro
    - ins: Thomas Smith
    target: https://yaml.org/spec/1.2.2/
  OAS:
    title: OpenAPI Specification 3.0.0
    date: 2017-07-26
    author:
    - ins: Darrel Miller
    - ins: Jeremy Whitlock
    - ins: Marsh Gardiner
    - ins: Mike Ralphson
    - ins: Ron Ratovsky
    - ins: Uri Sarid

informative:
  I-D.ietf-jsonpath-base:
  JSON-POINTER: RFC6901

--- abstract

This document registers
the application/foo media type
and the +foo structured syntax suffix
on the IANA Media Types registry.

--- middle

# Introduction

YAML [YAML] is a data serialization format that is widely used on the Internet,
including in the API sector (e.g. see [OAS]),
but the relevant media type and structured syntax suffix previously had not been registered by IANA.

To increase interoperability when exchanging YAML data
and leverage content negotiation mechanisms when exchanging
YAML resources,
this specification
registers the `application/foo` media type
and the `+foo` structured syntax suffix.

Moreover, it provides security considerations
and interoperability considerations related to [YAML],
including its relation with {{!JSON=RFC8259}}.

## Notational Conventions

{::boilerplate bcp14+}

This document uses the Augmented BNF defined in {{!RFC5234}} and updated
by {{!RFC7405}}.

The terms  "content", "content negotiation", "resource",
and "user agent"
in this document are to be interpreted as in {{!HTTP=RFC9110}}.

The terms "fragment" and "fragment identifier"
in this document are to be interpreted as in {{!URI=RFC3986}}.

The terms "node", "alias node", "anchor" and "named anchor"
in this document are to be intepreded as in [YAML].

## Fragment identification {#application-yaml-fragment}

This section describes how to use
alias nodes (see Section 3.2.2.2 and 7.1 of [YAML])
as fragment identifiers to designate nodes.

A YAML alias node can be represented in a URI fragment identifier
by encoding it into octects using UTF-8 {{!UTF-8=RFC3629}},
while percent-encoding those characters not allowed by the fragment rule
in {{Section 3.5 of URI}}.

If multiple nodes would match a fragment identifier,
the first such match is selected.

A fragment identifier is not guaranteed to reference an existing node.
Therefore, applications SHOULD define how an unresolved alias node
ought to be handled.

Users concerned with interoperability of fragment identifiers:

- SHOULD limit alias nodes to a set of characters
  that do not require encoding
  to be expressed as URI fragment identifiers:
  this is generally possible since
  named anchors are a serialization
  detail;
- SHOULD NOT use alias nodes that match multiple nodes.

In the example resource below, the URL `file.yaml#*foo`
references the alias node `*foo` pointing to the node with value `scalar`;
whereas
the URL `file.yaml#*bar` references the alias node `*bar` pointing to the node
with value `[ some, sequence, items ]`.

~~~ example
 %YAML 1.2
 ---
 one: &foo scalar
 two: &bar
   - some
   - sequence
   - items
~~~


# Media Type and Structured Syntax Suffix registrations

This section describes the information required to register
the above media type according to {{!MEDIATYPE=RFC6838}}

## Media Type application/yaml {#application-yaml}

The media type for YAML text is `application/foo`;
the following information serves as the registration form for this media type.

Type name:
: application

Subtype name:
: yaml

Required parameters:
: None

Optional parameters:
: None; unrecognized parameters should be ignored

Encoding considerations:
: binary

Security considerations:
: see {{sec}} of this document

Interoperability considerations:
: see {{int}} of this document

Published specification:
: [YAML]

Applications that use this media type:
: HTTP

Fragment identifier considerations:
: An empty fragment identifier references
  the root node.

  A fragment identifier starting with "*"
  is to be interpreted as a YAML alias node.

  A fragment identifier starting with "/"
  is to be interpreted as a JSON Pointer {{JSON-POINTER}}
  and is evaluated on the YAML representation graph,
  walking through alias nodes;
  this syntax can only reference YAML nodes that are
  on a path that is made up of nodes interoperable with
  the JSON data model.

Additional information:

- Deprecated alias names for this type:  application/x-yaml, text/yaml, text/x-yaml

- Magic number(s)  n/a

- File extension(s):  yaml, yml

- Macintosh file type code(s):  n/a

Person and email address to contact for further information:
: See Authors' Addresses section.

Intended usage:
: COMMON

Restrictions on usage:
: None.

Author:
: See Authors' Addresses section.

Change controller:
: n/a

## The +yaml Structured Syntax Suffix {#suffix-yaml}

The suffix
`+foo` MAY be used with any media type whose representation follows
that established for `application/foo`.
The media type structured syntax suffix registration form follows.
See {{MEDIATYPE}} for definitions of each of the registration form headings.

  Name:
  : YAML Ain't Markup Language (YAML)

  +suffix:
  :  +yaml

  References:
  :  [YAML]

  Encoding considerations:
  : see {{application-yaml}}

  Fragment identifier considerations:
  : Differently from `application/foo`,
    there is no fragment identification syntax defined
    for +yaml.

    A specific `xxx/yyy+foo` media type
    needs to define the syntax and semantics for fragment identifiers
    because the ones in {{application-yaml}}
    do not apply unless explicitly expressed.

  Interoperability considerations:
  : See {{application-yaml}}

  Security considerations:
  : See {{application-yaml}}

  Contact:
  : See Authors' Addresses section.

  Author:
  : See Authors' Addresses section

  Change controller:
  :  n/a

# Interoperability Considerations {#int}

## YAML is an Evolving Language {#int-yaml-evolving}

YAML is an evolving language and, over time,
some features have been added and others removed.



~~~ example
 %YAML 1.2
 ---
 non-json-keys:
   0: a number
   2020-01-01: a timestamp
   [0, 1]: a sequence
   ? {k: v}
   : a map
 non-json-value: 2020-01-01
~~~
{: title="Example of mapping keys not supported in JSON" #example-unsupported-keys}

# Security Considerations {#sec}

Security requirements for both media type and media type suffix
registrations are discussed in Section 4.6 of {{!MEDIATYPE=RFC6838}}.

## Arbitrary Code Execution {#sec-yaml-code-execution}

Care should be used when using YAML tags,
because their resolution might trigger unexpected code execution.

Code execution in deserializers should be disabled by default,
and only be enabled explicitly.
In those cases, the implementation should ensure - for example, via specific functions -
that the code execution results in strictly bounded time/memory limits.

Many implementations provide safe deserializers addressing these issues.



# IANA Considerations {#iana}

This specification defines the following new Internet media type {{MEDIATYPE}}.

IANA has updated the "Media Types" registry at <https://www.iana.org/assignments/media-types>
with the registration information provided below.

|--------------------------------------|---------------------------------------------|
| Media Type                           | Section                                     |
|--------------------------------------|---------------------------------------------|
| application/yaml                     | {{application-yaml}} of this document       |
|--------------------------------------|---------------------------------------------|

IANA has updated the "Structured Syntax Suffixes" registry at <https://www.iana.org/assignments/media-type-structured-suffix>
with the registration information provided below.

|--------------------------|------------------------------------------|
| Suffix                   | Section                                  |
|--------------------------|------------------------------------------|
| +yaml                    | {{suffix-yaml}} of this document         |
|--------------------------|------------------------------------------|

--- back

# Examples {#ex}

## Unreferenceable nodes

In this example, a couple of YAML nodes that cannot be referenced
based on the JSON data model
since their mapping keys are not strings.

~~~ example
 %YAML 1.2
 ---
 a-map-cannot:
   ? {be: expressed}
   : with a JSON Pointer

 0: no numeric mapping keys in JSON
~~~
{: title="Example of YAML nodes that are not referenceable based on JSON data model." #example-unsupported-paths}


# Acknowledgements

Thanks to Macy and Lea for being the initial contributors of this specification,
and to Alice and Bob for their support during the adoption phase.

In addition to the people above, this document owes a lot to the extensive discussion inside
and outside the  workgroup.
The following contributors have helped improve this specification by
opening pull requests, reporting bugs, asking smart questions,
drafting or reviewing text, and evaluating open issues:

John Foo
and Jason Bar.

# FAQ
{: numbered="false" removeinrfc="true"}

Q: Why this document?
:  Because it's useful :)


# Change Log
{: numbered="false" removeinrfc="true"}

RFC EDITOR PLEASE DELETE THIS SECTION.
