# Actions

Actions are how Kodex transforms or analyzes data. Actions can be called individually or as a sequence, in which each action will receive the result of the previous action. The following sections describe the different action types that Kodex currently supports.

## Generic Validation & Transformation

Often data is complex & hierarchical, making it difficult to apply simple transformations to it. For this case Kodex support generic form-based validation and transformation of data. This enables you to easily parse, validate and transform complex, hierarchical data like JSON documents. You can read more about this type of transformation in the [form action documenation]({{'actions.form'|href}}).

## Pseudonymization

Pseudonymization produces data that is no longer directly attributable to a specific individual. Using pseudonymous data lowers the risk of data processing for individuals and reduces the impact of data loss or data theft. It can be applied to direct or indirect identifiers as well as to a wide range of structured data types like numbers, dates, names or IP addresses. Some pseudonymization methods are based on reversible encryption, which makes it possible to de-pseudonymize the data again given knowledge of the key. Other methods like hashing are non-reversible. You can read more about this type of transformation in the [pseudonymization action documentation]({{'actions.pseudonymization'|href}})

## Encryption

Encryption produces data that is statistically indistinguishable from random noise and that can be decrypted only with knowledge of a secret encryption key. Kodex implements standard symmetric and asymmetric encryption techniques.

Please note that we consider encryption methods which produce the same ciphertext when given identical input data and encryption key(s) as pseudonymization methods, as the resulting data does (intentionally) not conform to modern security standards for encryption methods. Therfore, format-preserving encryption methods that operate without random initialization vectors (IV) are also considered pseudonymization methods under this approach.

## Anonymization

Anonymization actions use statistical techniques to produce data from which it it should not be possible to identify any specific individuals or infer any non-trivial information about these individual. Kodex relies on randomization and aggregation as anonymity mechanisms and produces anonymous data that conforms to modern anonymity standards like "differential privacy".

## Discovery

Discovery actions enable you to detect different types of personal or sensitive information in your structured and unstructured data items.

## Identification & Identity Management

Identification & identity management actions enable you to identify individuals based on various direct or indirect identifiers, and associate a permanent pseudonymous ID to every individual. This allows you to e.g. attribute data items from different sources to a single individual and use the pseudonymous ID of that individual to e.g. produce anonymous or pseudonymous data or verify the consent of that individual for a given data processing purpose.

## Consent Management

Consent management actions enable you to verify that a given individual has given consent for a specific data processing purpose. Together with the identification & identity management actions they enable you to build compliant data processing workflows and e.g. surpress data from individuals that have not given or withdrawn their consent for a specific processing purpose.

## Audit Logging

Audit logging actions enable you to trace the flow of data from individuals through your entire data processing infrastructure. They produce pseudonymous, searchable information about how data belonging to a specific individual was processed and where it was sent to, allowing you to e.g. retrieve, amend or delete this data.