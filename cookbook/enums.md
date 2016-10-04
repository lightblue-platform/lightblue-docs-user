# Enums

Enums belong to entityInfo part of metadata, which means using a particular schema version does not mean enum values won't change. Adding an enum value is considered a non breaking change and should be allowed. Removing or renaming an enum is a breaking change and should not be allowed.

Considering the above, you have 2 options when designing model classes in a language which supports enum types (such as java):
* don't use enum type, i.e. define enum metadata fields as strings in the model classes or
* implement enum types which support unknowns.

An example of a java enum with pojo2java nad java2pojo conversion support for the unknowns:

```java
public enum EnumType {
            enum1,
            enum2,
//          (...)
            unknown;

            private String value;

            private EnumType() {
                this.value = name();
            }

            public static EnumType createUnknown(String value) {
                EnumType t = EnumType.unknown;
                t.value = value;
                return t;
            }

            @JsonCreator
            public static EnumType fromValue(String value) {

                for (EnumType e : values()) {
                    if (value.equals(e.value)) {
                        return e;
                    }
                }

                // unknown enum
                return createUnknown(value);
            }

            @JsonValue
            public String toValue() {
                return value;
            }

        }
```

This implementation allows you to read an object with an unknown enum value from Lightblue and then save it back, without sacrificing type safty for known values.
