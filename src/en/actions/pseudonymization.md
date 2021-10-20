# Pseudonymization

Pseudonymization is a **privacy-enhancing technology (PET)** that replaces direct or indirect identifiers in a datapoint with other, "pseudonymous" values.
This protects the identity of the individual(s) that the data belongs to, as an adversary or a user of the data can no longer establish a relationship to a specific person.

Pseudonymization methods can be classified based on their properties. **Reversible pseudonymization methods** allow the de-pseudonymization of data, i.e. going back to the original identifier from the pseudonym with the help of additional data like a cryptographic key or a mapping table.
**Non-reversible pseudonymization methods** do not allow this. **Deterministic pseudonymization methods** will always create the same pseudonym when applied to the same identifier twice.
**Non-deterministic methods** will produce random pseudonyms even when applied to the same identifier multiple times.
**Structure-preserving pseudonymization methods** preserve specific internal structure of identifiers.
For example, a deterministic pseudonymization method is also structure-preserving in regards to the equality operator:
If the pseudonyms for two identifiers are equal, the original identifiers must have been identical too.
This principle can be extended to more advanced structural properties, as discussed below.
**Format-preserving pseudonymization methods** preserve the original format of the data.
For example, a format-preserving method could generate a valid zip code pseudonym from an original zip code.

## Reversible Methods

Kodex supports key-based reversible pseudonymization methods based on format-preserving encryption.
The resulting pseudonyms can be decrypted/depseudonymized using the original key. For example, the `merengue` pseudonymization method operates on arbitrary binary data and produces binary pseudonyms that can in addition preserve the prefix-structure of the data:

```yaml
- name: pseudonymize-name
  type: pseudonymize
  config:
    key: name
    method: merengue
```

### Structure-Preserving Methods

Based on the `merengue` method Kodex offers various structure-preserving pseudonymization methods that can operate on data types such as timestamps, IP addresses and numbers. 
These methods preserve the format of the original data and can also preserve structural information.
For example, the following actions perform structure- and format-preserving pseudonymization of the above-mentioned data types:


```yaml
- name: pseudonymize-date
  type: pseudonymize
  config:
    key: date
    method: structured
    type: date
    format: "%(2010-2030)Y-%m-%d"
- name: pseudonymize-ip
  type: pseudonymize
  config:
    key: ip
    method: structured
    type: ipv4
    preserve-prefixes: true
- name: pseudonymize-count
  type: pseudonymize
  config:
    key: count
    method: structured
    type: integer
    preserve-prefixes: true
    type-params:
      min: 0
      max: 10000
```

## Non-Reversible Methods

Kodex supports non-reversible pseudonymization methods as well, notably keyed hash-based pseudonymization based on keyed-hash message authentication codes (HMAC), as well as unkeyed hash-based pseudonymization (which we strongly discourage). For example, the following action generates an HMAC-based pseudonym:

```yaml
- name: pseudonymize-id
  type: pseudonymize
  config:
    key: id
    method: hmac
```
