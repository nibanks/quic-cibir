---
title: QUIC Connection ID Based Initial Routing
abbrev: QUIC-CIBIR
docname: draft-banks-quic-cibir-01
category: exp
date: 2022

stand_alone: yes

ipr: trust200902
area: Transport
kw: Internet-Draft

coding: us-ascii
pi: [toc, sortrefs, symrefs, comments]

author:
  -
    ins: N. Banks
    name: Nick Banks
    org: Microsoft Corporation
    email: nibanks@microsoft.com

--- abstract

This document defines an extension to the QUIC transport protocol to
consistently route all packets from a client to the appropriate server on a
shared UDP port.

--- middle

# Introduction

Several scenarios exist where multiple independent or isolated servers need to
run in the same environment, but cannot use independent local UDP ports.  For
instance, in server deployments that have hundreds or thousands of machines,
each with tens or hundreds of different QUIC servers running on them, the
server infrastructure may not be able to support the number of local UDP ports
it would require to give each server a unique one.  Additionally, because of
infrastucture requirements additional IP addresses may not be able to be used
as a solution either.

In these scenarios, the server infrastructure needs a way to essentially NAT
QUIC packets on a shared local UDP port between all servers using that port.
This document defines a mechanism for using QUIC connection IDs to encode the
necessary information for all client to server QUIC packets to be correctly
routed to the appropriate server.  A cooperating client can then use this to
specifically target a server on a shared port.

## Terms and Definitions

The keywords "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{!RFC2119}} {{!RFC8174}}
when, and only when, they appear in all capitals, as shown here.

# Specification

## Transport Parameter

Support for encoding CIBIR information is negotiated by means of a QUIC
Transport Parameter (name=cibir_encoding, value=0x30).  The cibir_encoding
transport parameter consists of two integer values (represented as
variable-length integers) that represent the length and offset to the
well-known idenfitier encoded into the client's source connection ID.

Servers that share a local UDP port using the CIBIR extension unconditionally
route received packets according to the CIBIR extension's protocol.  The
cibir_encoding transport parameter is used on the server side after the routing
has already happened to validate the intent of the client.  Servers MUST
validate the client sent the cibir_encoding transport parameter with the
matching offset and length that has been configured locally.  If the transport
parameter is missing or contains incorrect values the server MUST terminate the
connection with an error of type CONNECTION_REFUSED.

No special routing is done on the client side, but client MUST also validate
the server sent the cibir_encoding transport parameter with the matching offset
and length so as to verify the server is cooperating in the expected routing
scheme.  If the transport parameter is missing or contains incorrect values the
client MUST terminate the connection with an error of type
TRANSPORT_PARAMETER_ERROR.

## Packet Encoding and Routing

# Security Considerations

The client encodes well-known IDs in the QUIC connection ID that may expose
information to an observer.

# IANA Considerations

## QUIC Transport Parameter

This document registers a new value in the QUIC Transport Parameter Registry
maintained at
[](https://www.iana.org/assignments/quic/quic.xhtml#quic-transport).

Value:

: 0x30

Parameter Name:

: cibir_encoding

Status:

: permanent

Specification:

: This document

--- back
