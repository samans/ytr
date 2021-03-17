---
title: "YANG Transliterate Feature"
abbrev: "ytr"
docname: draft-mansfield-yang-tr-latest
category: info

ipr: trust200902
area: General
workgroup: TODO Working Group
keyword: Internet-Draft

stand_alone: yes
smart_quotes: no
pi: [toc, sortrefs, symrefs]

author:
 -
    ins: S. Mansfield
    name: Scott Mansfield
    organization: Ericsson
    email: scott.mansfield@ericsson.com

normative:
  RFC2119:
  TR:
     target: http://www.gnu.org/software/coreutils/manual/html_node/tr-invocation.html#tr-invocation
     title: GNU Coreutils TR
     author:
        ins: FSF
        name: Free Software Foundation
        org: Free Software Foundation
     date: 2020

informative:



--- abstract

TODO Abstract

--- middle

# Introduction

Transliteration is a common practice that replaces or deletes characters from a string.  There are several situations where transliteration (tr) would be useful when using the YANG data modeling language.  For example, whenever a string datatype is used as a key or when strings need to be compared, it would be useful to be able to specify a transliteration.  If a string pattern supports both upper and lower case, then if one string is in upper and one in lower in the compare, they will not match. This draft suggests adding a new substatement to support specifying a tr pattern for string datatypes.


# Conventions and Definitions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{RFC2119}} {{!RFC8174}}
when, and only when, they appear in all capitals, as shown here.

# The "Transliterate" Statement

The "transliterate" statement, which is an optional substatemment to the "type" statement, takes as an argument an ordered set of characters.  The characters will be used to transliterate the value of the string leaf when the leaf is retreived, is used as a key, or when the leaf is compared to another leaf.

The format of the transliteration follows the rules and formats found in {{TR}}.

## TR Examples
~~~~
   type string {
      pattern "[0-9a-fA-F]{2}(:[0-9a-fA-F]{2}){5}";
      transliterate "'[:lower:]' '[:upper:]'";
   }
~~~~
If the above string datatype definition is used:
* "12:34:ae" would return as 12:34:AE

To remove all potential ambiguity, consider the following example.  In the IETF mac-address uses:
~~~~
* pattern "'[0-9a-fA-F]{2}(:[0-9a-fA-F]{2}){5}’"
~~~~
and the IEEE uses:
~~~~
* pattern "'[0-9a-fA-F]{2}(-[0-9a-fA-F]{2}){5}'";
~~~~

So not only is the case of the letters ambiguous, so is the separator used.  The IETF uses ":" and the IEEE uses "-".  When comparing mac-addresses it is desirable to have semantically equivalent mac-address match.  So if an IETF formatted mac-address is compared with an IEEE mac-address even though the input patterns are different, they will match if they addresses are semantically identical.  For example:
* IETF: 12:34:af and IEEE: 12-34-AF should match.

Multiple transliterate statements are allowed and are processed first to last.

Using a transliterate statement like below, would allow the input to follow the pattern, but ensure semantic equivalent addresses match.

~~~~
   type string {
      pattern "[0-9a-fA-F]{2}(:[0-9a-fA-F]{2}){5}";
      transliterate "'[:lower:]' '[:upper:]'";
      transliterate "-d '\-:'";
   }
~~~~

That pattern will take any valid input that matches the pattern and return a normalized format that can be compared.  So:
*12:34:ae would transliterate to 1234AE

The input can be in the desired format.  A NETCONF feature to turn on or turn off the transliteration is TBD.
 

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.



--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
