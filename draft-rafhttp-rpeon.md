---
title: Resource Addressing for HTTP
abbrev: RAFHTTP
docname: draft-rafhttp-rpeon
category: info

ipr: trust200902
area: General
workgroup: http
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
 -
    ins: "R. Peon"
    name: "Roberto Peon"
    organization: "Facebook"
    email: fenix@fb.com

normative:
  RFC2119:

informative:



--- abstract

Resource Addressing for HTTP.

--- middle

# Introduction

It has become a common pattern that infrequently-accessed resources accessed by a subset of users are served from a subset of locations across the world. To fix the mismatch between address abstraction which presupposes all resources exist at any resolution of a hostname and this implementation reality, web page creators have had to resort to using large numbers of subdomains, raw IPs, redirects, or proxying. All of these mechanisms come with potential costs, whether in latency, cache pollution/hit rate, inefficient network use, or complexity.

These issues could be solved by offering a policy-based abstraction for addressing of HTTP resources, freeing the web page authors from needing to encode the mechanism of distribution into the page, while simultaneously providing freedom for operations teams to move, replicate and distribute web content as appropriate to their goals.

RAFHTTP is such a policy-based abstraction, with a simple, regular policy language that is expressive enough for most use-cases while restricted enough to prevent the issues that come with unconstrained or Turing-complete languages.

# Policy fields

The policy consists of the following fields:
  * policy-name
  * restricted-glob-match
  * domain
  * origin
  * client-target
  * refetch-after
  * valid-until
  * policy-action
  * signature

## Policy-name
 The policy name is a URI

## Resource-glob-match

The restricted-glob-match is used to determine which, if any, resource being fetched by the user-agent are to use the policy.

### Repeats allowed

Multiple restricted-glob-match fields are allowed in a policy.

### Allowed expressions

A restricted glob is a glob with only one star allowed, and then only as the last element of the match.
If the domain or origin of the glob does not match the origin that is proved to be authoritative by the signature, then the policy MUST be rejected in its entirety.

### When does a URL match?

If the URL being requested by the user-agent matches a restricted glob, then the policy applies.
If multiple policies apply, the policy with the longest match wins.

### Why restricted globs?

Restricted glob is chosen over regular expression as a matching mechanism because it is possible to create order-independent rulesets, representable as a trie, and executable in O(k) time, where k is the length of the URL. This would be difficult, if not impossible if using regular expressions.

## Policy-action

Policy-action defines how the host should attempt to access the resource, and is consists of an expression using the following policy language:

### Operations

The policy language consists of s-expressions which describe operations.

#### Built-in expressions
The following s-expressions are built-in:

  * '\*' \[child\] \[child\] \[child\*\]
  * '+' \[child\] \[child\] \[child\*\]
  * '-' \[child\] \[child\] \[child\*\]
  * '/' \[child\] \[child\]
  * 'race' \[options\] \[child\*\]
  * 'timeout' \[options\] \[child\]
  * 'delay' \[options\] \[child\]
  * 'random' \[child\]
  * 'http1' \[options\] \[child\]
  * 'http2' \[options\] \[child\]
  * 'https1' \[options\] \[child\]
  * 'https2' \[options\] \[child\]

##### '\*'  '+' '-' '/'
Arithmetic operations. Return floats.

##### race
race \[max-concurrency=int\] \[num-until-exhausted=int\] \[num-until-done=int\]

max-effort?
health?

##### timeout

timeout seconds=float

##### delay

delay seconds=float

##### random

random range=float

##### http

http resolvable

##### https

http resolvable

#### Named fields:

Before evaluating the policy, the user-agent may substitute numbers into named fields.
Named fields are represented in the policy string with an open-curly, a string of alpha-numeric characters, and a close-curly. No spaces are allowed.

## Example policy:
(policy-name some-uri)
(restricted-glob-match https://example.com/foo/\*)
(restricted-glob-match https://example.com/bar/\*)
(restricted-glob-match https://bar.example.com/bar/\*)

# Conventions and Definitions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{RFC2119}} {{!RFC8174}}
when, and only when, they appear in all capitals, as shown here.


# Security Considerations

Computational attacks

Memory-consumption attacks

Retargeting attacks

Exposing information

Untrusted 3rd parties


# IANA Considerations

This document has no IANA actions.



--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
