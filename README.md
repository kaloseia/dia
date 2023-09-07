# Dia - Application Data Modelling Specification

v0.0.1

## Introduction

Simple, intuitive base data modelling specification for the Kaloseia ecosystem. This specification represents the foundation that will allow for the creation of a multi-stack, technology-agnostic application management framework in subsequent projects.

The primary goal is the creation of a centralized, declarative data modelling format that can be utilized by both technical and non-technical stakeholders to specify standardized business application data, that can then be generatively transpiled into stack-specific technologies.

## Models

Models are the core data structure. They are analogous to SQL tables.

Each model consists of a `name`, a set of `fields`, a set of `identifiers`, and `related` models.

*Simple Example:* `address.mod`
```yaml
name: Address
fields:
  UUID:
    type: UUID
    attributes:
      - immutable
  Street:
    type: String
  HouseNr:
    type: String
  ZipCode:
    type: String
  City:
    type: String
identifiers:
  primary: UUID
  addr:
    - Street
    - HouseNr
    - ZipCode
    - City
related:
  User:
    type: ForOne
  Document:
    type: HasMany
```

### Fields

Denoted by the `fields:` key, the model fields are specified as a uniquely named key (example: `Street:`) and the finer-grained field configuration.

#### Atomic Types

* UUID: A RFC-4122 compatible UUID string.
* AutoIncrement: A classic numeric, auto-incrementable record ID.
* String: A variable-length string.
* Integer: A numeric value for zero, whole numbers, and their negative counterparts.
* Float: A numeric floating-point decimal value.
* Time: A timestamp value with a UTC offset.
* Date: A timestamp value as a date with a UTC offset and zero time values.
* Protected: An encryptable and decryptable value for sensitive information such as API keys.
* Sealed: A hashable value that can not be decrypted (typically passwords).

#### Supported Field Attributes

* `immutable`: Immutable fields that will never change across the application lifecycle.
* `mandatory`: Fields that must be filled during creation and cannot be emptied during the record's lifecycle.

### Identifiers

Denoted by the `identifiers:` key, this section always requires a `primary:` identifier with a corresponding field name.

Composite or secondary identifier groups can be added with a unique key (i.e. `addr:`), and a list of relevant fields.

### Related

Denoted by the `related:` key, this section lists all related models. This means both dependencies and dependents (prerequisites). 

This is an atypical design decision, made for full system transparency and flexibility, with the assumption that configuration errors are easily detectable with the correct tooling.

Each relationship is characterized by an `ownership`, as in the directionality of the relationship, a `cardinality` that differentiates between `1` and `n` related entities, and optional `polymorphism` if the related model types are multivariate and unknown.

#### Supported Ownership Values

* `For`: The current model is "for" related models, when the current model is a dependent on the existence of related models.
  * *Example:* "Address ForOne User": Address is reliant on the existence of a specified User, because Address explicitly references User identifiers.
* `Has`: The current model "has" related models, when the current model is required by the related models.
  * *Example:* "Address HasMany Document": Address supports the reliance of Documents on the existence of specified Addresses, because Documents explicitly reference Address identifiers.

#### Supported Cardinality Values

* `One`
* `Many`
