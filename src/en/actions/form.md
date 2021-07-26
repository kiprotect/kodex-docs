# Form Action

The `form` action implements our open-source [form validation & transformation library](https://github.com/kiprotect/go-helpers), enabling you to parse, validate and transform complex, hierarchical data.

## Getting Started

A **form** consists of a number of **fields**. Each field can define one or more **validators**, which can validate and transform the content of the field. If the form validation is successful, the action will return the validated fields. If not, an error will be thrown. Here's an example form action specification:

```yaml
type: form
config:
  fields:
    - name: date
      validators:
        - type: IsString
        - type: IsTime
          config:
            format: rfc3339
    - name: count
      validators:
        - type: IsInteger
          config:
            hasMin: true
            min: 0
            hasMax: true
            max: 1000
```

This action will ensure each item has a `date` field that contains an RFC-3339 formatted data string and a `count` field that contains an integer value between 0 and 1000. There are many other validator types available, here's the full list:

* **CanBeAnything**: Just ensures the field is present, but does not perform any other validation on it.
* **IsBoolean**: Ensures the field contains a boolean value, i.e. `true` or `false`.
* **IsBytes**: Ensures the field contains a byte array with a given `encoding`, e.g. `hex`, `base64` or `base64-url` encoded data.
* **IsFloat**: Ensures the field contains a float value. Optionally, if `hasMin` or `hasMax` is `true`, the `min` or `max` config options define the range of the float value.
* **IsHex**: Ensures the field contains a hex value. If `convert` is `true`, the value will be converted to a byte array.
* **IsIn**: Ensures the field contains one of the specified `choices`.
* **IsInteger**: Ensure the filed contains an integer value. Optionally, if `hasMin` or `hasMax` is `true`, the `min` or `max` config options define the range of the integer value.
* **IsList**: Ensures the field contains a list of values. Optionally, a list of `validators` will be applied to each list element.
* **IsNotIn**: Like **IsIn**, but ensures the value of the field is **not** in the given `choices`.
* **IsOptional**: Will skip the other validators if the field is undefined, making it optional.
* **IsString**: Ensures the field contains a string value. Optionally, `minLength` and `maxLength` specify the minimum and maximum length of the string.
* **IsStringList**: Ensures the field contains a list of strings. Optionally, a list of `validators` will be applied to each list element.
* **IsStringMap**: Ensures the field contains a string-based hash map / dictionary. Optionally, a `form` can be specified that will be used to validate the value.
* **IsTime**: Ensures the field contains a datetime/time object in the given `format`, either `rfc3339`, `rfc3339-date`, `unix`, `unix-nano` or `unix-milli`. If `raw` is true the string will not be converted to a `Time` object. If `toUTC` is true, the resulting `Time` object will be converted to `UTC`.
* **IsUUID**: Ensures the field contains a UUID value.
* **MatchesRegex**: Ensures the field matches the given regular expression. Assumes that the field contains a string value, which should be validated before using the `IsString` validator.
* **Or**: Ensures the field contains one of several possible values as specified by a list of `options`, which in turn contain a list of validators. Will stop at the first option that successfully validates the value.
* **Switch**: Validates the field against a list of validators defined in a `cases` string map, where the chosen case depends on the value of another field whose name is given by `key`.

## Actions Within Forms

In addition, the form mechanism supports all other actions as well through the `IsAction` validator, which takes an action specification as configuration. Example validator:

```yaml
type: IsAction
config:
  type: pseudonymize
  config:
    method: merengue
```

This performs a pseudonymization of the value using the `merengue` method. Parameters of embedded actions are managed automatically by the form action.