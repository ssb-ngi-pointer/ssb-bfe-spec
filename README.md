# SSB binary field encodings

Shortname: SSB-BFE

Definition of binary encodings for common types such as message ids
and feed ids to be used in binary feed formats. This can be seen as an
extension of the types defined in [TFK].

The binary encoding is defined as the concatenation of:
- the `type` of thing as a UInt8
- the `format` of the `type` as a UInt8
- the `data` for the actual bytes

## Codes

 | type code   | referencing        |
 | ----------- | -------------      |
 | 0           | identity           |
 | 1           | message            |
 | 2           | blob               |
 | 3           | diffie-hellman key |
 | 4           | signature          |
 | 5           | box encoding       |
 | 6           | value types        |

For canonical, machine-readable definitions, see `binary-field-encodings.json`

### Identity type

Sometimes also know as feeds. Since this encompasses identities that
does not create messages themselves, such as fusion identities
we will use the name identity instead.

| Type | format code | format name     | specification    |
|------|-------------|-----------------|------------------|
| 0    | 0           | classic         | [classic]        |
| 0    | 1           | gabby grove     | [gabby grove]    |
| 0    | 2           | bamboo          | [bamboo]         |
| 0    | 3           | metafeed        | [metafeed]       |
| 0    | 4           | fusion identity | [fusionidentity] |

Example:

String encoding of a classic feed:

```
  @6CAxOI3f+LUOVrbAl0IemqiS7ATpQvr9Mdw9LC4+Uv0=.ed25519
  │└─────────────────────┬────────────────────┘└───┬──┘
 sigil           base64 encoded key             suffix
```

### Message type

| Type | format code | format name   | specification   |
|------|-------------|---------------|-----------------|
| 1    | 0           | classic       | [classic]       |
| 1    | 1           | gabby grove   | [gabby grove]   |
| 1    | 2           | cloaked group | [private group] |
| 1    | 3           | bamboo        | [bamboo]        |
| 1    | 4           | metafeed      | [metafeed]      |

The data part of a null message must be be encoded as 0 bytes in the
length of the hash. For a classic message that would be
`0x10 00000000000000000000000000000000`.

Example:

String encoding of a classic message id:

```
  %R8heq/tQoxEIPkWf0Kxn1nCm/CsxG2CDpUYnAvdbXY8=.sha256
  │└─────────────────────┬────────────────────┘└───┬─┘
 sigil           base64 encoded key             suffix
```

Binary encoding (as hex) of the same thing:

```
  01 00  47 c8 5e ab fb 50 a3 11 08 3e 45 9f d0 ac 67 d6 70 a6 fc 2b 31 1b 60 83 a5 46 27 02 f7 5b 5d 8f
   │  │  └────────────────────┬────────────────────────────────────────────────────────────────────────┘
type  │                hex encoded key
     format 
```

### Blob type

| Type | format code | format name | specification |
|------|-------------|-------------|---------------|
| 2    | 0           | classic     | [classic]     |

Example: 

String encoding of a classic blob id:

```
  &S7+CwHM6dZ9si5Vn4ftpk/l/ldbRMqzzJos+spZbWf4=.sha256
  │└─────────────────────┬────────────────────┘└───┬─┘
 sigil           base64 encoded key             suffix
```

### Diffie-hellman type

| Type | format code | format name | specification |
|------|-------------|-------------|---------------|
| 3    | 0           | curve25519  |               |

### Signature type

| Type | format code | format name | specification |
|------|-------------|-------------|---------------|
| 4    | 0           | ed25519     |               |

Example: 

String encoding of a classic ed25519 signature:

```
  nkY4Wsn9feosxvX7bpLK7OxjdSrw6gSL8sun1n2TMLXKySYK9L5itVQnV2nQUctFsrUOa2istD2vDk1B0uAMBQ==.sig.ed25519
  └─────────────────────────────────────┬────────────────────────────────────────────────┘└────┬─────┘
                             base64 encoded signature                                        suffix
```

### Box encoding type

| Type | format code | format name | specification |
|------|-------------|-------------|---------------|
| 5    | 0           | box1        |               |
| 5    | 1           | box2        |               |

### Value type

| Type | format code | format name      | specification                 |
|------|-------------|------------------|-------------------------------|
| 6    | 0           | utf8 string      |                               |
| 6    | 1           | boolean          | false 0x060100, true 0x060101 |
| 6    | 2           | null / undefined | 0x0602                        |

Value type can be used for binary encodings that has limited support
for different types such as [bencode].

[TFK]: https://github.com/ssbc/envelope-spec/blob/master/encoding/tfk.md
[classic]: https://ssbc.github.io/scuttlebutt-protocol-guide/#message-format
[gabby grove]: https://github.com/ssbc/ssb-spec-drafts/tree/master/drafts/draft-ssb-core-gabbygrove/00
[bamboo]: https://github.com/AljoschaMeyer/bamboo
[private group]: https://github.com/ssbc/private-group-spec
[metafeed]: https://github.com/ssb-ngi-pointer/bipfy-badger-spec
[fusionidentity]: https://github.com/ssb-ngi-pointer/fusion-identity-spec/
[bencode]: https://en.wikipedia.org/wiki/Bencode
