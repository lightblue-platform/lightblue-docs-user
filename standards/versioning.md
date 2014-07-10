# Versioning
The metadata service enforces some standards around versioning.  The standard, rational for the standard, and how the standard are enfoced are documented here.

## Standard: Common
The key words "MUST", "MUST NOT", "SHOULD", "SHOULD NOT", and "MAY" in this document are to be interpreted as described in RFC 2119.

* All versions MUST follow Semantic Versioning 2.0.0 except where deviations are documented in this standard.
* Pre-release versions MUST NOT be used in non-development environments.
* A pre-release version of "SNAPSHOT" MAY be used as a basis for artifacts not ready for release.
* Versions without a pre-release are called "production versions" in this documentation.

## Standard: Application
* All API artifacts MUST NOT include a PATCH component to the version.
* All artifacts MUST be versioned together where API artifacts take the MAJOR and MINOR versions only.

## Standard: Metadata
* Version uniqueness MUST be guaranteed per entity.
* The first production version of a metadata MUST be 1.0.0.
* Metadata PATCH version MUST be greater than the maximum PATCH extended unless MAJOR or MINOR version has been increased.
* If metadata MINOR version is greater than the maximum MINOR extended version:
    * PATCH version must be 0 (zero).
* If metadata MAJOR version is greater than the maximum MAJOR extended version:
    * MINOR version must be 0 (zero).
    * PATCH version must be 0 (zero).

## Rational: Application
Why API doens't include PATCH:
* Keeping APIs as Major.Minor allows us to keep api versions unchanged for implementation only updates.
* Any API artifact MUST not contain business logic.
* Public documentation is independent of API source.

If the API changes in any way a minor or major version change is required.  This is an deliberate decision and is intended to protection clients of the service.

## Rational: Metadata
Each entity's metadata is versioned in isolation to others, therefore versions are unique to that entity, not to all metadata.

## Process: Metadata
Note this does not account for collision of versions when there are multiple extensions of one entity.  The simplest example is where two different development streams require patching the same version independently:
* 1.0.0
    * 1.0.1-SNAPSHOT
    * 1.0.1-SNAPSHOT

In the case above the 1.0.1-SNAPSHOT versions are not unique.

### Create
1. Create new entity, initial version is 0.1.0-SNAPSHOT
1. Develop new entity...
1. Cut release version 1.0.0

### Update: PATCH
1. Create new metadata for entity extending version A.B.C with version A.B.Z-SNAPSHOT where:
    * Z = C + 1
1. Develop new version...
1. Cut release version A.B.Z

### Update: MINOR
1. Create new metadata for entity extending version A.B.C with version A.Y.0-SNAPSHOT where:
    * Y = B + 1
1. Develop new version...
1. Cut release version A.Y.0

### Update: MAJOR
1. Create new metadata for entity extending version A.B.C with version X.0.0-SNAPSHOT where:
    * X = A + 1
1. Develop new version...
1. Cut release version X.0.0

### Update: MERGE
1. Create new metadata for entity extending versions A.B.C and D.E.F with version X.Y.Z-SNAPSHOT where:
    * X = IF (A=D) THEN A ELSE max(A,D) + 1
    * Y = IF (A<>D) THEN 0 ELSE IF (B=E) THEN B ELSE max(B,E) + 1
    * Z = IF (A<>D OR B<>E) THEN 0 ELSE max(C,F) + 1
1. Develop new version...
1. Cut release version X.Y.Z

## Enforcement: Application
* Within the original lightblue code this standard will be strictly followed.
* Pull requests must follow this standard to be accepted, but forks cannot be controlled.

## Enforcement: Metadata
Enforcement will be done by application code.  Within the context of an entity:
* Metadata version MUST follow standard documented here.
* Metadata MUST NOT extend any metadata if no other versions are defined.
* Metadata MUST extend at least one version if at least one other version is defined.
* Metadata extensions MUST be backwards compatible with all metadata with status "active" or "deprecated".
* If a certain entity appears more than once in the tree formed by entity metadata references, all appearances must have the same version. For instance, if A -> B -> C_1 and  A -> C_2, then the version of C_1 must be the same as the version of C_2.
