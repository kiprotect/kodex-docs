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

## Reference

Like all actions, the `pseudonymize` method requires the `config` parameter, which in turn requires a `key` parameter that specifies the attribute that should be pseudonymized as well as a `method` parameter that specifies the pseudonymization method. Currently the following methods are supported:

* `merengue`: Non-structure & non-format preserving, reversible, encryption-based pseudonymization.
* `structured`: Structure- & format-preserving, reversible, encryption-based pseudonymization.
* `hmac`: Non-structure & non-format preserving, non-reversible, authenticated hash-based pseudonymization.

Depending on the chosen method, additional configuration parameters are necessary, as described below.

### Merengue Pseudonymization Parameters

The `merengue` method accepts an `encode` parameter that specifies the encoding of the resulting byte string. Currently the only possible value (and the default) is `base64`.


### Structured Pseudonymization Parameters

The `structured` method accepts the following parameters:

* `preserve-prefixes`: If *true*, will preserve the prefixes of a structured data value. For example, when using the `date` format, dates with a common prefix (e.g. the same year and month) will be mapped to pseudonyms that also share a prefix of the same length. Default to *false*.
* `type`: Specifies the data type to pseudonymize. Must be one of `ip`, `date`, `integer`, `ipv4` or `ipv6`.
* `type-params`: Specifies additional type parameters depending on the chosen type. Currently, only the `integer` type requires mandatory `min` and `max` type parameters that specify its range.
* `format`: Specifies the type-dependent format of the data to be pseudonymized (if applicable). Currently, only the `data` type supports the `format` parameter, as described below.

#### Date Format

The `date` type can parse and produce dates in various formats. The `format` specifier is modeled after the standard Unix time format, using `%` characters followed by a format code. The following codes are supported:

* `Y`: The year as a four-digit number (e.g. 2021)
* `m`: The month as a two-digit number (e.g. 01 or 12)
* `d`: The day of the month as a two digit number (e.g. 03 or 30)
* `H`: The hour of the time as a two digit number (e.g. 11 or 07)
* `M`: The minute of the time as a two digit number (e.g. 01 or 59)
* `S`: The second of the time as a two digit number (e.g. 01 or 59)
* `n`: The nanoseconds of the time as a nine digit number (e.g. 000000001)
* `z`: The time zone of the time as a signed four digit number (e.g. +0400 or - 0730)

In addition to the common syntax, the format also allows specifying ranges, which need to be provided in parentheses after the `%` sign. For example, `%Y(2001-2022)` limits the year to values between 2001 and 2022. Specifying ranges can be useful to ensure that pseudonymized timestamp are within an expected range. However, this requires that all input values be in that range as well.