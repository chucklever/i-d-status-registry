---
title: "A Registry of Network File System Version 4 Status Codes"
abbrev: "NFSv4 Status Code Registry"
category: std

docname: draft-cel-nfsv4-status-registry-latest
submissiontype: IETF
ipr: trust200902
updates: 8178
stand_alone: yes
pi: [toc, sortrefs, symrefs, docmapping]
v: 3
area: "Web and Internet Transport"
workgroup: "Network File System Version 4"
keyword:
 - NFS
 - nfsstat4
 - NFS4ERR
 - IANA

venue:
  group: "Network File System Version 4"
  type: "Working Group"
  mail: "nfsv4@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/nfsv4/"
  github: "chucklever/i-d-status-registry"
  latest: "https://chucklever.github.io/i-d-status-registry/draft-cel-nfsv4-status-registry.html"

author:
 -
    fullname: Charles Lever
    role: editor
    country: United States of America
    email: cel-ietf@chucklever.net

normative:
  RFC7120:
  RFC7530:
  RFC7862:
  RFC5662:
  RFC8126:
  RFC8178:
  RFC8276:
  RFC8881:
  I-D.cel-nfsv4-fattr4-registry:

informative:
  RFC1813:
  RFC4506:
  RFC7531:
  RFC7863:

--- abstract

NFS version 4 status codes are identified by numeric values that
no IANA registry records, so protocol extensions under development
at the same time can assign the same value to different status
codes.  This document requests an IANA registry of NFSv4 status
codes, populates it from published RFCs, and requires future status
codes to obtain their values through it.  It updates RFC 8178.


--- middle

# Introduction {#intro}

{{RFC7530}}, {{RFC8881}}, and {{RFC7862}} together specify three
minor versions of the Network File System (NFS) version 4 protocol.
Within these specifications, a common protocol element is the
nfsstat4 XDR data type, which carries the result of each operation
in a COMPOUND or CB_COMPOUND procedure and the result of the
procedure as a whole.

The nfsstat4 data type is an XDR enum.  Its values are the status
code NFS4_OK, indicating success, and a set of NFS4ERR_ codes, each
of which identifies a distinct failure condition.  Every NFSv4
operation specifies the subset of these codes it may return.

The above specifications (and others) assign a numeric value to
each status code.  The numeric value is the value of the code's XDR
enum constant, and so identifies the code on the wire.

{{RFC8178}} specifies a set of permitted mechanisms to extend the
NFSv4 protocol, but no registry records which status code values
have been assigned.  Assignment of new values is currently handled
ad hoc by the nfsv4 Working Group, and authors of concurrent
extensions can select the same value for different status codes.

It has been additionally observed that there is no single place
that maps each status code to the RFC (or RFCs) where that code is
specified.  The Working Group assignment mechanism is not how
protocol-element-to-numeric-value mappings are commonly handled
elsewhere.

To address these concerns, this document requests the creation
of a new IANA registry so that value assignment is managed via
a well-known and authoritative mechanism.  The document provides
an initial set of entries for this new registry based on values
in published RFCs to date.  Lastly, a new assignment mechanism
is normatively specified for nfsstat4, updating {{RFC8178}}.

# Requirements Language

{::boilerplate bcp14-tagged}

# Allocation of Status Code Values {#allocation}

## Background

Each NFSv4 status code is identified by a non-negative integer that
is the value of the corresponding XDR enum constant (for example,
NFS4ERR_NOENT has the value 2).  The values fall into two ranges.
Values below 10000 were chosen by {{RFC7530}} and its predecessors
to match the values of the corresponding NFS version 3 status codes
{{RFC1813}}, which in turn match traditional errno values.  Only
those NFSv3 codes that have an NFSv4 counterpart were carried
forward, so this range is sparse.  Values of 10001 and above
identify conditions that have no errno counterpart and have been
assigned sequentially as the protocol has grown.

{{Section 4.2 of RFC8178}} permits the addition of new, previously
unused, values to existing enums as an XDR extension, and
{{Section 6 of RFC8178}} requires that such an extension to an
existing minor version be published as a Proposed Standard.
{{RFC8178}} also forbids the deletion or reuse of an enum value
once assigned.  However, {{RFC8178}} does not say how the numeric
value for a new status code is chosen.  In practice, an author of
an Internet-Draft selects the next value after the highest one the
author is aware of.  When two drafts are in progress concurrently,
both may select the same value, and the collision is discovered
only when one of the documents is revised or when implementations
of the two features are combined.

## The Registry {#mechanism}

This document creates the "NFSv4 Status Codes" registry (see
{{iana}}) as the single authoritative source of nfsstat4 value
assignments.  From the publication of this document onward:

* As required by {{Section 6 of RFC8178}}, a new status code is
  specified in a document published as a Proposed Standard.  The
  registration policy for the registry is therefore Standards Action
  with Expert Review ({{Sections 4.5 and 4.9 of RFC8126}}), which
  admits exactly that class of document and matches the policy of
  the other NFSv4 registries created by {{Section 22 of RFC8881}}.
  The role of the Designated Expert is described in {{expert}}.

* The document that specifies a new status code MUST include an
  IANA Considerations section requesting that the code be added to
  the registry, and MUST obtain the code's numeric value through
  that request.  A document MUST NOT specify an XDR enum constant
  whose value differs from the registered value.

* To eliminate collisions between concurrently developed extensions,
  values MAY be allocated before publication using the early
  allocation procedure of {{RFC7120}}.  A Working Group document
  that introduces a new status code SHOULD request early allocation
  once the Working Group has adopted it, and MUST NOT use a value
  that has not been either registered or early-allocated.

* Values are assigned sequentially, starting from the lowest
  unassigned value of 10001 or above.  Values below 10000 are not
  available for assignment: they exist only to mirror NFSv3 status
  codes, and no new NFSv3 codes are expected.  IANA MUST NOT assign
  a value that has been previously assigned, even if the
  corresponding status code has since been obsoleted, because
  {{Section 4.2 of RFC8178}} prohibits reuse of an enum value.

* A status code is never removed from the registry.  A document
  that deprecates a status code updates the registry entry to
  record the deprecation and the deprecating document (see
  {{Section 6 of RFC8126}} for status terminology), but the value
  remains allocated.

* Each registry entry records the minor version in which the status
  code was introduced.  Per {{Section 8.2 of RFC8178}}, a status
  code introduced in one minor version is available in all later
  minor versions unless a later minor version explicitly removes
  it.  {{Section 15.1.16 of RFC8881}} and {{Section 2 of RFC5662}}
  do exactly that for several NFSv4.0 codes; such a code remains
  valid in the minor version that introduced it, so its registry
  entry is not marked deprecated or obsolete.

## Designated Expert Guidance {#expert}

The Designated Expert's review is confined to the registration
itself, not the merits of the status code, which are for the IETF
consensus process that produced the Proposed Standard.[^ednote]
The expert confirms that:

* The requested value is the lowest unassigned value of 10001 or
  above, or was previously early-allocated to the same document.

* The name is unique within the registry and follows the naming
  conventions in {{iana-registry}}.

* The Reference column identifies the section of the requesting
  document that specifies the status code, and any additions the
  requesting document makes to the Reference column of an existing
  entry satisfy the selection rule in {{iana-registry}}.

For a document that deprecates or obsoletes an existing status code,
the expert additionally confirms that:

* The document states which minor versions the change applies to.

* The requested Status value is consistent with that scope.  A code
  withdrawn from every minor version in which it is available is
  marked "Deprecated" or "Obsolete", and the document is added to
  the Reference column.  A code withdrawn from some minor versions
  but still valid in others keeps an empty Status, and only the
  Reference column changes.

* The document does not reuse the code's value or renumber the
  code, and does not request that the entry be removed.

[^ednote]: Editor's note: skeletal; to be expanded if the Working
    Group wants the expert to check more than registry hygiene.

## Updates to RFC 8178 {#updates-8178}

This document updates {{RFC8178}} as follows.

{{Section 4.2 of RFC8178}} describes the addition of new, previously
unused, values to existing enums as an XDR extension.  This document
adds the requirement that, for the nfsstat4 enum, those values be
allocated from the "NFSv4 Status Codes" registry as specified in
{{mechanism}}, rather than being chosen by the author of the
extension.

{{Section 6 of RFC8178}} continues to govern the publication
requirements for an extension that introduces a new status code.
The registry does not relax those requirements; it only records the
outcome of the process.

Nothing in this document changes the rules in {{RFC8178}} for XDR
extensions other than nfsstat4 values, nor the rules for creating
new minor versions.

# Security Considerations {#security}

This document creates an IANA registry and specifies how values
in that registry are allocated.  It introduces no new protocol
behavior and therefore no new security considerations beyond those
of the documents that define the individual status codes.

# IANA Considerations {#iana}

## NFSv4 Parameters Registry Group {#iana-group}

{{I-D.cel-nfsv4-fattr4-registry}} requests the creation of a
registry group titled "Network File System Version 4 (NFSv4)
Parameters".  IANA is requested to place the registry described in
{{iana-registry}} in that group.  If the group does not exist when
this document is processed, IANA is requested to create it.

## NFSv4 Status Codes Registry {#iana-registry}

IANA is requested to create a new registry titled "NFSv4 Status
Codes" within the group described in {{iana-group}}.

The registry has the following columns:

Value:
: The status code's numeric value, which is the value of its XDR
  enum constant.  Values are non-negative integers.

Name:
: The status code's name, which is the name of its XDR enum
  constant as it appears in the specifying document.  The name of
  a code that indicates failure is upper case and is prefixed with
  "NFS4ERR_" (for example, NFS4ERR_NOENT).  A document that
  registers a new status code MUST follow this convention.  The
  only code that does not indicate failure is NFS4_OK.

Minor Version:
: The NFSv4 minor version in which the status code was first
  specified.

Reference:
: The sections of the documents that normatively specify the
  status code, selected as follows.  For each minor version
  specification that defines the code, the code's definition
  section is cited.  If a later minor version specification
  removes the code from use, the section that does so is cited as
  well.  No other section of a minor version specification is
  cited.  A section of any other document is cited only if it
  formally updates the defining specification with respect to the
  status code.  A statement whose subject is an operation, and
  which mentions the code only as one of that operation's possible
  results, does not qualify.

Status:
: Empty for a status code in current use.  Otherwise one of
  "Deprecated", "Obsolete", or "Reserved" (see {{Section 6 of
  RFC8126}}).  "Deprecated" and "Obsolete" are set by a later
  document that changes the code's status, and that document is
  added to the Reference column.  "Reserved" marks a value that is
  not available for assignment.

The registration policy for this registry is Standards Action with
Expert Review ({{Sections 4.5 and 4.9 of RFC8126}}).  Guidance for
the Designated Expert is in {{expert}}.  Early allocation of values
is permitted per {{RFC7120}}.  Values are assigned sequentially from
the lowest unassigned value of 10001 or above.  Assigned values
MUST NOT be reused, and entries MUST NOT be removed (see
{{mechanism}}).

## Initial Registry Contents {#iana-initial}

The initial contents of the registry are the status codes specified
in {{RFC7530}}, {{RFC8881}}, {{RFC7862}}, and {{RFC8276}}.  The XDR
enum constants for the base minor versions appear in the companion
XDR descriptions {{RFC7531}}, {{RFC5662}}, and {{RFC7863}}.  For
status codes introduced in NFSv4.0, both the NFSv4.0 and NFSv4.1
specifications are referenced, because each minor version
specification restates the code.  For the NFSv4.0 codes that
{{Section 15.1.16 of RFC8881}} removes from use in NFSv4.1, that
section is referenced in place of a definition section.
NFS4ERR_RESOURCE is likewise removed from use in NFSv4.1, but only
by a comment in the XDR description, so {{Section 2 of RFC5662}} is
referenced for it.

Two values are marked Reserved.  Value 10002 was skipped by NFSv4
because the NFSv3 status code with that value, NFS3ERR_NOT_SYNC
{{RFC1813}}, has no NFSv4 counterpart.  Value 10073 was left unused
by {{RFC5662}}.  The Status column is empty for every other initial
entry.

| Value | Name | Minor Version | Reference | Status |
|------:|------|---------------|-----------|--------|
| 0 | NFS4_OK | 4.0 | {{Section 13.1.3.1 of RFC7530}}, {{Section 15.1.3.1 of RFC8881}} | |
| 1 | NFS4ERR_PERM | 4.0 | {{Section 13.1.6.2 of RFC7530}}, {{Section 15.1.6.2 of RFC8881}} | |
| 2 | NFS4ERR_NOENT | 4.0 | {{Section 13.1.4.8 of RFC7530}}, {{Section 15.1.4.8 of RFC8881}} | |
| 5 | NFS4ERR_IO | 4.0 | {{Section 13.1.4.6 of RFC7530}}, {{Section 15.1.4.6 of RFC8881}} | |
| 6 | NFS4ERR_NXIO | 4.0 | {{Section 13.1.4.11 of RFC7530}}, {{Section 15.1.16.3 of RFC8881}} | |
| 13 | NFS4ERR_ACCESS | 4.0 | {{Section 13.1.6.1 of RFC7530}}, {{Section 15.1.6.1 of RFC8881}} | |
| 17 | NFS4ERR_EXIST | 4.0 | {{Section 13.1.4.3 of RFC7530}}, {{Section 15.1.4.3 of RFC8881}} | |
| 18 | NFS4ERR_XDEV | 4.0 | {{Section 13.1.4.14 of RFC7530}}, {{Section 15.1.4.12 of RFC8881}} | |
| 20 | NFS4ERR_NOTDIR | 4.0 | {{Section 13.1.2.6 of RFC7530}}, {{Section 15.1.2.6 of RFC8881}} | |
| 21 | NFS4ERR_ISDIR | 4.0 | {{Section 13.1.2.3 of RFC7530}}, {{Section 15.1.2.3 of RFC8881}} | |
| 22 | NFS4ERR_INVAL | 4.0 | {{Section 13.1.1.4 of RFC7530}}, {{Section 15.1.1.4 of RFC8881}} | |
| 27 | NFS4ERR_FBIG | 4.0 | {{Section 13.1.4.4 of RFC7530}}, {{Section 15.1.4.4 of RFC8881}} | |
| 28 | NFS4ERR_NOSPC | 4.0 | {{Section 13.1.4.9 of RFC7530}}, {{Section 15.1.4.9 of RFC8881}} | |
| 30 | NFS4ERR_ROFS | 4.0 | {{Section 13.1.4.13 of RFC7530}}, {{Section 15.1.4.11 of RFC8881}} | |
| 31 | NFS4ERR_MLINK | 4.0 | {{Section 13.1.4.7 of RFC7530}}, {{Section 15.1.4.7 of RFC8881}} | |
| 63 | NFS4ERR_NAMETOOLONG | 4.0 | {{Section 13.1.7.3 of RFC7530}}, {{Section 15.1.7.3 of RFC8881}} | |
| 66 | NFS4ERR_NOTEMPTY | 4.0 | {{Section 13.1.4.10 of RFC7530}}, {{Section 15.1.4.10 of RFC8881}} | |
| 69 | NFS4ERR_DQUOT | 4.0 | {{Section 13.1.4.2 of RFC7530}}, {{Section 15.1.4.2 of RFC8881}} | |
| 70 | NFS4ERR_STALE | 4.0 | {{Section 13.1.2.7 of RFC7530}}, {{Section 15.1.2.7 of RFC8881}} | |
| 10001 | NFS4ERR_BADHANDLE | 4.0 | {{Section 13.1.2.1 of RFC7530}}, {{Section 15.1.2.1 of RFC8881}} | |
| 10002 | | | | Reserved |
| 10003 | NFS4ERR_BAD_COOKIE | 4.0 | {{Section 13.1.1.2 of RFC7530}}, {{Section 15.1.1.2 of RFC8881}} | |
| 10004 | NFS4ERR_NOTSUPP | 4.0 | {{Section 13.1.1.5 of RFC7530}}, {{Section 15.1.1.5 of RFC8881}} | |
| 10005 | NFS4ERR_TOOSMALL | 4.0 | {{Section 13.1.1.7 of RFC7530}}, {{Section 15.1.1.7 of RFC8881}} | |
| 10006 | NFS4ERR_SERVERFAULT | 4.0 | {{Section 13.1.1.6 of RFC7530}}, {{Section 15.1.1.6 of RFC8881}} | |
| 10007 | NFS4ERR_BADTYPE | 4.0 | {{Section 13.1.4.1 of RFC7530}}, {{Section 15.1.4.1 of RFC8881}} | |
| 10008 | NFS4ERR_DELAY | 4.0 | {{Section 13.1.1.3 of RFC7530}}, {{Section 15.1.1.3 of RFC8881}} | |
| 10009 | NFS4ERR_SAME | 4.0 | {{Section 13.1.11.4 of RFC7530}}, {{Section 15.1.15.4 of RFC8881}} | |
| 10010 | NFS4ERR_DENIED | 4.0 | {{Section 13.1.8.4 of RFC7530}}, {{Section 15.1.8.3 of RFC8881}} | |
| 10011 | NFS4ERR_EXPIRED | 4.0 | {{Section 13.1.5.3 of RFC7530}}, {{Section 15.1.5.4 of RFC8881}} | |
| 10012 | NFS4ERR_LOCKED | 4.0 | {{Section 13.1.8.5 of RFC7530}}, {{Section 15.1.8.4 of RFC8881}} | |
| 10013 | NFS4ERR_GRACE | 4.0 | {{Section 13.1.9.1 of RFC7530}}, {{Section 15.1.9.2 of RFC8881}} | |
| 10014 | NFS4ERR_FHEXPIRED | 4.0 | {{Section 13.1.2.2 of RFC7530}}, {{Section 15.1.2.2 of RFC8881}} | |
| 10015 | NFS4ERR_SHARE_DENIED | 4.0 | {{Section 13.1.8.10 of RFC7530}}, {{Section 15.1.8.9 of RFC8881}} | |
| 10016 | NFS4ERR_WRONGSEC | 4.0 | {{Section 13.1.6.3 of RFC7530}}, {{Section 15.1.6.3 of RFC8881}} | |
| 10017 | NFS4ERR_CLID_INUSE | 4.0 | {{Section 13.1.10.1 of RFC7530}}, {{Section 15.1.13.2 of RFC8881}} | |
| 10018 | NFS4ERR_RESOURCE | 4.0 | {{Section 13.1.3.4 of RFC7530}}, {{Section 2 of RFC5662}} | |
| 10019 | NFS4ERR_MOVED | 4.0 | {{Section 13.1.2.4 of RFC7530}}, {{Section 15.1.2.4 of RFC8881}} | |
| 10020 | NFS4ERR_NOFILEHANDLE | 4.0 | {{Section 13.1.2.5 of RFC7530}}, {{Section 15.1.2.5 of RFC8881}} | |
| 10021 | NFS4ERR_MINOR_VERS_MISMATCH | 4.0 | {{Section 13.1.3.2 of RFC7530}}, {{Section 15.1.3.2 of RFC8881}} | |
| 10022 | NFS4ERR_STALE_CLIENTID | 4.0 | {{Section 13.1.10.2 of RFC7530}}, {{Section 15.1.13.5 of RFC8881}} | |
| 10023 | NFS4ERR_STALE_STATEID | 4.0 | {{Section 13.1.5.6 of RFC7530}}, {{Section 15.1.16.5 of RFC8881}} | |
| 10024 | NFS4ERR_OLD_STATEID | 4.0 | {{Section 13.1.5.5 of RFC7530}}, {{Section 15.1.5.5 of RFC8881}} | |
| 10025 | NFS4ERR_BAD_STATEID | 4.0 | {{Section 13.1.5.2 of RFC7530}}, {{Section 15.1.5.2 of RFC8881}} | |
| 10026 | NFS4ERR_BAD_SEQID | 4.0 | {{Section 13.1.8.2 of RFC7530}}, {{Section 15.1.16.1 of RFC8881}} | |
| 10027 | NFS4ERR_NOT_SAME | 4.0 | {{Section 13.1.11.3 of RFC7530}}, {{Section 15.1.15.3 of RFC8881}} | |
| 10028 | NFS4ERR_LOCK_RANGE | 4.0 | {{Section 13.1.8.8 of RFC7530}}, {{Section 15.1.8.7 of RFC8881}} | |
| 10029 | NFS4ERR_SYMLINK | 4.0 | {{Section 13.1.2.8 of RFC7530}}, {{Section 15.1.2.8 of RFC8881}} | |
| 10030 | NFS4ERR_RESTOREFH | 4.0 | {{Section 13.1.4.12 of RFC7530}}, {{Section 15.1.16.4 of RFC8881}} | |
| 10031 | NFS4ERR_LEASE_MOVED | 4.0 | {{Section 13.1.5.4 of RFC7530}}, {{Section 15.1.16.2 of RFC8881}} | |
| 10032 | NFS4ERR_ATTRNOTSUPP | 4.0 | {{Section 13.1.11.1 of RFC7530}}, {{Section 15.1.15.1 of RFC8881}} | |
| 10033 | NFS4ERR_NO_GRACE | 4.0 | {{Section 13.1.9.2 of RFC7530}}, {{Section 15.1.9.3 of RFC8881}} | |
| 10034 | NFS4ERR_RECLAIM_BAD | 4.0 | {{Section 13.1.9.3 of RFC7530}}, {{Section 15.1.9.4 of RFC8881}} | |
| 10035 | NFS4ERR_RECLAIM_CONFLICT | 4.0 | {{Section 13.1.9.4 of RFC7530}}, {{Section 15.1.9.5 of RFC8881}} | |
| 10036 | NFS4ERR_BADXDR | 4.0 | {{Section 13.1.1.1 of RFC7530}}, {{Section 15.1.1.1 of RFC8881}} | |
| 10037 | NFS4ERR_LOCKS_HELD | 4.0 | {{Section 13.1.8.6 of RFC7530}}, {{Section 15.1.8.5 of RFC8881}} | |
| 10038 | NFS4ERR_OPENMODE | 4.0 | {{Section 13.1.8.9 of RFC7530}}, {{Section 15.1.8.8 of RFC8881}} | |
| 10039 | NFS4ERR_BADOWNER | 4.0 | {{Section 13.1.11.2 of RFC7530}}, {{Section 15.1.15.2 of RFC8881}} | |
| 10040 | NFS4ERR_BADCHAR | 4.0 | {{Section 13.1.7.1 of RFC7530}}, {{Section 15.1.7.1 of RFC8881}} | |
| 10041 | NFS4ERR_BADNAME | 4.0 | {{Section 13.1.7.2 of RFC7530}}, {{Section 15.1.7.2 of RFC8881}} | |
| 10042 | NFS4ERR_BAD_RANGE | 4.0 | {{Section 13.1.8.1 of RFC7530}}, {{Section 15.1.8.1 of RFC8881}} | |
| 10043 | NFS4ERR_LOCK_NOTSUPP | 4.0 | {{Section 13.1.8.7 of RFC7530}}, {{Section 15.1.8.6 of RFC8881}} | |
| 10044 | NFS4ERR_OP_ILLEGAL | 4.0 | {{Section 13.1.3.3 of RFC7530}}, {{Section 15.1.3.4 of RFC8881}} | |
| 10045 | NFS4ERR_DEADLOCK | 4.0 | {{Section 13.1.8.3 of RFC7530}}, {{Section 15.1.8.2 of RFC8881}} | |
| 10046 | NFS4ERR_FILE_OPEN | 4.0 | {{Section 13.1.4.5 of RFC7530}}, {{Section 15.1.4.5 of RFC8881}} | |
| 10047 | NFS4ERR_ADMIN_REVOKED | 4.0 | {{Section 13.1.5.1 of RFC7530}}, {{Section 15.1.5.1 of RFC8881}} | |
| 10048 | NFS4ERR_CB_PATH_DOWN | 4.0 | {{Section 13.1.12.1 of RFC7530}}, {{Section 15.1.11.4 of RFC8881}} | |
| 10049 | NFS4ERR_BADIOMODE | 4.1 | {{Section 15.1.10.1 of RFC8881}} | |
| 10050 | NFS4ERR_BADLAYOUT | 4.1 | {{Section 15.1.10.2 of RFC8881}} | |
| 10051 | NFS4ERR_BAD_SESSION_DIGEST | 4.1 | {{Section 15.1.12.2 of RFC8881}} | |
| 10052 | NFS4ERR_BADSESSION | 4.1 | {{Section 15.1.11.1 of RFC8881}} | |
| 10053 | NFS4ERR_BADSLOT | 4.1 | {{Section 15.1.11.2 of RFC8881}} | |
| 10054 | NFS4ERR_COMPLETE_ALREADY | 4.1 | {{Section 15.1.9.1 of RFC8881}} | |
| 10055 | NFS4ERR_CONN_NOT_BOUND_TO_SESSION | 4.1 | {{Section 15.1.11.6 of RFC8881}} | |
| 10056 | NFS4ERR_DELEG_ALREADY_WANTED | 4.1 | {{Section 15.1.14.1 of RFC8881}} | |
| 10057 | NFS4ERR_BACK_CHAN_BUSY | 4.1 | {{Section 15.1.12.1 of RFC8881}} | |
| 10058 | NFS4ERR_LAYOUTTRYLATER | 4.1 | {{Section 15.1.10.3 of RFC8881}} | |
| 10059 | NFS4ERR_LAYOUTUNAVAILABLE | 4.1 | {{Section 15.1.10.4 of RFC8881}} | |
| 10060 | NFS4ERR_NOMATCHING_LAYOUT | 4.1 | {{Section 15.1.10.5 of RFC8881}} | |
| 10061 | NFS4ERR_RECALLCONFLICT | 4.1 | {{Section 15.1.14.3 of RFC8881}} | |
| 10062 | NFS4ERR_UNKNOWN_LAYOUTTYPE | 4.1 | {{Section 15.1.10.9 of RFC8881}} | |
| 10063 | NFS4ERR_SEQ_MISORDERED | 4.1 | {{Section 15.1.11.8 of RFC8881}} | |
| 10064 | NFS4ERR_SEQUENCE_POS | 4.1 | {{Section 15.1.3.10 of RFC8881}} | |
| 10065 | NFS4ERR_REQ_TOO_BIG | 4.1 | {{Section 15.1.3.8 of RFC8881}} | |
| 10066 | NFS4ERR_REP_TOO_BIG | 4.1 | {{Section 15.1.3.6 of RFC8881}} | |
| 10067 | NFS4ERR_REP_TOO_BIG_TO_CACHE | 4.1 | {{Section 15.1.3.7 of RFC8881}} | |
| 10068 | NFS4ERR_RETRY_UNCACHED_REP | 4.1 | {{Section 15.1.3.9 of RFC8881}} | |
| 10069 | NFS4ERR_UNSAFE_COMPOUND | 4.1 | {{Section 15.1.3.12 of RFC8881}} | |
| 10070 | NFS4ERR_TOO_MANY_OPS | 4.1 | {{Section 15.1.3.11 of RFC8881}} | |
| 10071 | NFS4ERR_OP_NOT_IN_SESSION | 4.1 | {{Section 15.1.3.5 of RFC8881}} | |
| 10072 | NFS4ERR_HASH_ALG_UNSUPP | 4.1 | {{Section 15.1.13.4 of RFC8881}} | |
| 10073 | | | | Reserved |
| 10074 | NFS4ERR_CLIENTID_BUSY | 4.1 | {{Section 15.1.13.1 of RFC8881}} | |
| 10075 | NFS4ERR_PNFS_IO_HOLE | 4.1 | {{Section 15.1.10.6 of RFC8881}} | |
| 10076 | NFS4ERR_SEQ_FALSE_RETRY | 4.1 | {{Section 15.1.11.7 of RFC8881}} | |
| 10077 | NFS4ERR_BAD_HIGH_SLOT | 4.1 | {{Section 15.1.11.3 of RFC8881}} | |
| 10078 | NFS4ERR_DEADSESSION | 4.1 | {{Section 15.1.11.5 of RFC8881}} | |
| 10079 | NFS4ERR_ENCR_ALG_UNSUPP | 4.1 | {{Section 15.1.13.3 of RFC8881}} | |
| 10080 | NFS4ERR_PNFS_NO_LAYOUT | 4.1 | {{Section 15.1.10.7 of RFC8881}} | |
| 10081 | NFS4ERR_NOT_ONLY_OP | 4.1 | {{Section 15.1.3.3 of RFC8881}} | |
| 10082 | NFS4ERR_WRONG_CRED | 4.1 | {{Section 15.1.6.4 of RFC8881}} | |
| 10083 | NFS4ERR_WRONG_TYPE | 4.1 | {{Section 15.1.2.9 of RFC8881}} | |
| 10084 | NFS4ERR_DIRDELEG_UNAVAIL | 4.1 | {{Section 15.1.14.2 of RFC8881}} | |
| 10085 | NFS4ERR_REJECT_DELEG | 4.1 | {{Section 15.1.14.4 of RFC8881}} | |
| 10086 | NFS4ERR_RETURNCONFLICT | 4.1 | {{Section 15.1.10.8 of RFC8881}} | |
| 10087 | NFS4ERR_DELEG_REVOKED | 4.1 | {{Section 15.1.5.3 of RFC8881}} | |
| 10088 | NFS4ERR_PARTNER_NOTSUPP | 4.2 | {{Section 11.1.2.4 of RFC7862}} | |
| 10089 | NFS4ERR_PARTNER_NO_AUTH | 4.2 | {{Section 11.1.2.3 of RFC7862}} | |
| 10090 | NFS4ERR_UNION_NOTSUPP | 4.2 | {{Section 11.1.1.1 of RFC7862}} | |
| 10091 | NFS4ERR_OFFLOAD_DENIED | 4.2 | {{Section 11.1.2.1 of RFC7862}} | |
| 10092 | NFS4ERR_WRONG_LFS | 4.2 | {{Section 11.1.3.2 of RFC7862}} | |
| 10093 | NFS4ERR_BADLABEL | 4.2 | {{Section 11.1.3.1 of RFC7862}} | |
| 10094 | NFS4ERR_OFFLOAD_NO_REQS | 4.2 | {{Section 11.1.2.2 of RFC7862}} | |
| 10095 | NFS4ERR_NOXATTR | 4.2 | {{Section 8.3.1 of RFC8276}} | |
| 10096 | NFS4ERR_XATTR2BIG | 4.2 | {{Section 8.3.2 of RFC8276}} | |
{: #nfsstat4-initial title="Initial contents of the NFSv4 Status Codes registry"}

Values 10097 and above are unassigned.


--- back

# Open Issues {#open-issues}

This section is to be removed before publishing as an RFC.

Each item below is tracked as an issue in this document's issue
tracker, where the detail and the discussion live.

* Whether values 10002 and 10073 should be marked Reserved, as this
  revision does, or simply left unassigned.

* Several section headings in {{RFC8881}} carry the wrong numeric
  value: {{Section 15.1.5.2 of RFC8881}} (NFS4ERR_BAD_STATEID, 10025,
  shown as 10026), {{Section 15.1.3.12 of RFC8881}}
  (NFS4ERR_UNSAFE_COMPOUND, 10069, shown as 10068), and
  {{Section 15.1.16.3 of RFC8881}} (NFS4ERR_NXIO, 6, shown as 5).
  The registry uses the values from the XDR in {{RFC5662}}.  Errata
  should be filed if none exist.

# Acknowledgments
{:numbered="false"}

Thanks to Éric Vyncke for suggesting this approach.

The editor is grateful to
Bill Baker,
Greg Marsden,
and
Martin Thomson
for their input and support.

Special thanks to
Area Director
Gorry Fairhurst,
NFSv4 Working Group Chair
Brian Pawlowski,
and
NFSv4 Working Group Secretary
Thomas Haynes
for their guidance and oversight.
