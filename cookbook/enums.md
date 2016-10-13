# Enums

Enums belong to entityInfo part of metadata, which means using a particular schema version does not mean enum values won't change. Adding an enum value is considered a non breaking change and should be allowed. Removing or renaming an enum is a breaking change and should not be allowed.

Considering the above, you have 2 options when designing model classes in a language which supports enum types (such as java):
* don't use enum type, i.e. define enum metadata fields as strings in the model classes or
* implement enum types which support unknowns.

An example of a java enum with unknown support:

```java
    public static enum EnumType {
        enum1,
        enum2,
//      (...)
        _unknown;

        private static final Log log = LogFactory.getLog(EnumType.class);

        @JsonCreator
        public static EnumType fromValue(String value) {
            try {
                return EnumType.valueOf(value);
            } catch (Exception e) {
                log.warn("Unrecognized enum "+value+", using unknown", e);
                return EnumType._unknown;
            }
        }
    }
```

NOTE: This implementation allows you to read an object with an unknown enum value from Lightblue, but does not allow you to save it back. The string type solution does not have this limitation, but lacks type safety.
