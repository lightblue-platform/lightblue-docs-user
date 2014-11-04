# Overview
The objective of this document is to show both how json schema works and how we are using it.  As the spec [1] is currently in draft this document is subject to radical change in the future.  There are some problems with json schema, so hopefully updates will come along eventually to fix things.
> Note: There is a [reference section](../standards/json_schema.html#references) in the end of this page with the links cited using '[ ]'

There is some official documetation on the standard [2] but It might not be enough without practical examples. The same official page also provide some examples [3] (some that might be even more helpeful are the advanced examples[4]. As it is quite new and the newest schema still in draft, there is an active group of user discussion about json schema [5] as well a special tag in StackOverflow [6] that can help to clear a some questions. As the fast wide spread of usage of json schema due adoption in many projects, some of them also wrote some documentation and examples about JSON schema that can be used as complementary study, such as spacetelescope [7], usingjsonschema[8] and jsonary [9] and many other that you may find in the internet. It is strongly recommend to study how the json schema works for best usage of Lightblue. You can try out some online tools for help, like jsonschema.net [10] for example.

There might be some things about json schema that are not intuitive, so this documentation will outline them here.  This section doesn't aim to define the Lightblue use of the schema, so look to later sections for that detail.

In summary, a json schema is just a json document that conforms to the schema specification [1].  For Lightblue purposes, it defined some resources (files) in the project.  Each schema can be referenced by other schemas.  Or you can reference a subset of the schema.

#JSON Schema
Since a schema is just a json document it is composed of objects and fields with values.  This section will go through the main components here and make note of the bits that were either hard to find to figure out or will be key for later sections for our standard.

##Identity
Each schema is identified by the URI at which it can be located.  That means you can have local and remote schemas.  This identity is important from the schema trying to do the reference, but you can specify an identity in the target with the "id" attribute as noted in a later section.  For local schema resources, it is relative to the root of the classpath and includes up through the schema name.  For example, if you had a schema in a directory called "foo" on the classpath and the schema was called "myschema.json" you could reference it with "/foo/myschema.json#".  The "#" in the reference indicates the root for a schema.  From there you can reference individual elements, which will be covered later.  If you had a second schema in the "foo" directory it could take a shortcut and do a relative reference of "myschema.json#".

##Attributes
All of these attributes are optional.  You won't have an empty schema, so perhaps you can say at least one complex child is required (object), but you can mix and match them as necessary.

###"id"
An attribute at the root level of the document and on each of the major sub-objects, id should be usable as a reference to the schema or sub-object.  As the JSON schema specification still not in a final version this may not work. But there are other ways to do references.  The value in the "id" should probably start with "#" to indicate it's relative to the root of the schema.  Given it has no real value it can be left off of everything.

###"$schema"
Attribute indicating what json schema the schema defined in the resource conforms to.  The current version is v4-draft (http://json-schema.org/draft-04/schema#).  Recommend setting this so your schema won't default to the current version, which may or may not be backwards compatible.

###"type"
Attribute indicating the type of entity being represented.  Is important on properties to indicate the type of content allowed, but if omitted it appears to default to a value of "object".

###"properties"
An object that captures a bag of properties that are top level attributes exposed by the schema.  That means in a json document validated against a schema you can use each of the properties as top level attributes in that document.  You can nest properties inside properties.  In the simplest schema you'll have properties at the top level and each child object defines a single property.  The name of the child object is the name of the property and there are some attributes you can set on the child to control the behavior:

* description - a description of the property, can always be present
* enum - an array of strings enumerating a concrete set of values allowed in this property
* type - can be simple types (e.g. "string", "integer") or complex types (e.g. "object", "array")
* format - can further restrict the format of simple types.  For example: "regex".
* minItems - when type is array, integer value indicates the minimum number of items required.
* maxItems - when type is array, integer value indicates the maximum number of items allowed.
* items - defines the types of contents the array can have.  This can be single object types or multiple.
* oneOf | anyOf | not - value is an array of objects defining what is allowed.  Will be explained with examples in the standards later.
* $ref - value of this attribute is a reference to another schema or subset of a schema.  Will explain in more detail later.

For a full set of what is allowed see the json schema [1].

####Example: References
References to to a property can be made by name and can be relative to a resource as noted in the "Identity" section of this document.  See how the property "target" is referenced by the property "source" and definition "origin" in this example below:
```
{
    "$schema":"http://json-schema.org/draft-04/schema#",
    "properties":{
        "target":{
            "type":"string"
        },
        "source":{
            "$ref":"#/properties/target"
        }
    },
    "definitions":{
        "origin":{
            "$ref":"#/properties/target"
        }
    }
}
```

###"patternProperties"
Similar to properties but allows defining patterns (regex) for child object names instead of specifying a concrete string value.  All other rules for properties apply.

###"additionalProperties"
A peer to properties and/or patternProperties, default value is false.  If value is true, then only the properties defined in the properties section or that match the patternProperties section are allowed in the schema.  Any document that has extra properties will fail validation.  The thing to remember about this attribute is that it applies ONLY to immediate properties.  It does not apply to properties pulled in via anyOf or allOf.

###"required"
A peer to properties.  An string array of property names that are required by this schema.

###"definitions"
Basically the same as properties, but these are not available for use in a json document without being referenced by something in a schema's properties.  Think of it as definitions for objects that can be referenced / used elsewhere.  Also can be useful to break complex structures into smaller definitions so they're easier to manage, reference, and reuse.  Each child object follows the same rules as properties documented in an earlier section.

####Example: References
References to to a definition can be made by name and can be relative to a resource as noted in the "Identity" section of this document.  See how the definition "target" is referenced by the property "source" and the definition "origin" in this example below.
```
{
    "$schema":"http://json-schema.org/draft-04/schema#",
    "properties":{
        "source":{
            "$ref":"#/definitions/target"
        }
    },
    "definitions":{
        "target":{
            "type":"string"
        },
        "origin":{
            "$ref":"#/definitions/target"
        }
    }
}
```

###"anyOf"
An array of schemas.  Any number (one or more) of them can be matched.

###"oneOf"
An array of scheams.  Only one can match.

###"allOf"
An array of scheams.  All of them must match.

#Our Standard
The main reason we're defining a standard for json schema is to ensure it's easy to consume by others.  The spec is pretty flexible, which can lead to very hard to read code.  These standards are in place in an attempt to ensure it can be read easily by consumers of the service in the future.  It is broken up into two sections.  The first is for attribute requirements, the second section is based on functionality.

##Attributes

###"id"
Don't bother setting an id attribute on anything at this time.  It serves no functional purpose.  Will revisit in the future if there is some actual value.

###"$schema"
Always set a value for $schema.  At this time, the value that should be used is "http://json-schema.org/draft-04/schema#"

###"documentation"
In general, always try to include a description for all properties defined.  An exception is if the property simply references another schema.  In that case it may be acceptable to not supply a definition because the assumption is that the referenced schema will have one.  But if there are quirks with the reference itself document it as a part of that reference as a description!

###"type"
Set a type for all schemas else code generation won't work.  For "$ref" do not set a type.

There are many simple types defined in the json schema.  To use them, simply specify a "type" attribute with a value of http://json-schema.org/draft-04/schema#/definitions/simpleTypes [1].  For quick reference, they are:
"array"
"boolean"
"integer"
"null"
"number"
"object"
"string"

Note that further documentation is provided for each that warrants it here.

###"type":"array"
There are some additional attributes you can specify with an array.

"minItems" - Set if the min is something other than 0.  For arrays typically the expectation is 2 or more values if a non-array version of the items is allowed.
"maxItems" - the maximum number of items, there is no default.
"items" - the types of contents the array can have.  This can be single object types or multiple.

###"type":"object"
With an object you can have child:
properties
additionalProperties
required

If setting additional properties to false take care that considerations for schema inheritance are taken.

###"type":"string"
Can optionally restrict the format with a "format" attribute but not all tools support it and those that do may have differing interpretations of each value.  Some of the canonical set of values accepted are:
regex
uri

##Functionality

###Schema Files
As noted in the "Identity" section above you can reference other schemas as a local or remote resource.  The standard will be to use local references for our artifacts until there is a need discovered for external references.  This will therefore drive how we break schemas apart.  Here's a swag at some rules

Each json schema file will use an extesion of .json and reside in a "json-schema" directory on the classpath.  Additional sub-directories are acceptable as noted in rules below.

For purposes of this section, "schema" is defined as a set of properties defined in a "properties" attribute.
If the schema can be used by itself it should be defined in a separate .json file.
Chunks of schema functionality that cannot be used by themselves should be created as a definition.
Definitions that are shared across multiple schema files should be captured in a "common.json" schema.  This file should not contain any top level properties.
Schema files that can be grouped into a common purpose should be put into a sub-directory with a name based on that purpose.
Common purpose schemas that need a common grouping functionality (similar to XSD choice) can define a separate "choice.json" schema.  This schema has a single definition named based on the purpose (same as directory name).
Groups of schemas can be futher grouped together with additional directory hierarchy.

Schema names can take the context of the directory path as assumed part of the name.  The name of each schema should be picked to be as simple as possible and to represent the purpose of that schema.  Multiple words should be separated by a single dash, all file names should be lower case.

###Enumeration
Because we would like to generate java code in some cases from JSON Schemas we're limited in what can be done in enumerations.  The spec for schema allows any values in an enum, but enums in java must conform to java naming standards.  See: http://docs.oracle.com/javase/tutorial/java/nutsandbolts/variables.html#naming

Simple Content vs Array
This area is subject to change.  It isn't used enough to know if we want to continue with this path of dual support or to simply go with single object or array min items of 1.

If your property can have 1 or more items use oneOf to create an option of picking either a single instance of the item or an array of the item.

The examples in this section will build off of each other, hence the ordering may not make sense at first glance.

####Example: 1 only
Setting up a simple reference to a definition that is local to illustrate how you don't have to define the property contents directly as part of the property and setting up for further examples that will build on this.

```
{
    "$schema":"http://json-schema.org/draft-04/schema#",
    "properties":{
        "field":{
            "$ref":"#/definitions/item_content"
        }
    },
    "definitions":{
        "item_content":{
            "type":"string"
        }
    }
}
```

####Example 2 or more
An array requiriting 2 or more items, which reference the same definition used in the "1 only" example.

```
{
    "$schema":"http://json-schema.org/draft-04/schema#",
    "properties":{
        "field":{
            "type":"array",
            "minItems":2,
            "items":{
                "$ref":"#/definitions/item_content"
            }
        }
    },
    "definitions":{
        "item_content":{
            "type":"string"
        }
    }
}
```

####Example 1 or more
Merges the functionality of the "1 only" and "1 or more" examples.  Note the reuse of the item reference within the oneOf construct.  This is where the reference becomes most useful to have so the structure doesn't get defined more than once.

```
{
    "$schema":"http://json-schema.org/draft-04/schema#",
    "properties":{
        "field":{
            "oneOf":[
                {
                    "$ref":"#/definitions/item_content"
                },
                {
                    "type":"array",
                    "minItems":2,
                    "items":{
                        "$ref":"#/definitions/item_content"
                    }
                }
            ]
        }
    },
    "definitions":{
        "item_content":{
            "type":"string"
        }
    }
}
```

###Schema Inheritance
Glossary, to help this make more sense:

* extension - a schema that has extended one or more schemas
* extended schema - a schema that has been extended by another schema (extension)

When defining the properties for a schema you can do a pseudo-inheritiance.  Use the `allOf` construct to indicate all the schemas that you wish to inherit from.  If you want to set `additionalProperties` to `false` you must define the properties on the schema that is using `allOf`.  The `additionalProperties` attribute applies only to immediate (local) properties.  This has two impacts on this pseudo-inheritiance:
* the extended schemas will fail validation against a json document if `additionalProperties` is false and there are new properties introduced in the extension.
* the extension will fail validation if specifying `additionalProperties` as false unless all properties from the extended schemas are also included in the extension.

This means:
* for schemas that are intended for extension, do not set `additionalProperties` to false.
* extensions include the extended schema's properties in properties.  You don't have to redefine the property, just include it with an empty body in the properties of the extension.

See the example for how this works.

####PROBLEMS
If you remove a property from the extended schema the extension still has that property defined.  Defaults for the property are now applied and schema validation will still expect the property.  This means to remove a property from an extended schema you have to also be sure to remove it from all extensions.

####Example: Inheritance
In this example the `extension` property extends the `common` definition.  Note that `additionalProperties` is set to `false`.  This means that all properties from `common` must be defined on `extension`.  As you can see in the example, the one property `name` is defined with an empty body.

```
{
    "$schema":"http://json-schema.org/draft-04/schema#",
    "properties":{
        "extension":{
            "allOf":[
                {
                    "$ref":"#/definitions/common"
                }
            ],
            "additionalProperties":false,
            "properties":{
                "name":{},
                "nickname":{
                    "type":"string"
                }
            }
        }
    },
    "definitions":{
        "common":{
            "properties":{
                "name":{
                    "type":"string"
                }
            }
        }
    }
}
```

#Tools

##Validation
The best way to validate is to write a unit test, but sometimes you want to do a quick check.  For those times you can use JSON Schema syntax validation[11].  For example, validating the example schemas on this document was done in this online tool.

###Abstract Java Test Class
Maven dependencies:
```
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.2.3</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.2.3</version>
</dependency>
<dependency>
    <groupId>com.github.fge</groupId>
    <artifactId>json-schema-validator</artifactId>
    <version>2.1.7</version>
</dependency>
```

Only putting this here because the current source is likely to move.  This is the abstract class all of the current json schema unit tests use:
```
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.nio.charset.Charset;
import java.util.Iterator;

import junit.framework.Assert;

import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.github.fge.jsonschema.exceptions.ProcessingException;
import com.github.fge.jsonschema.main.JsonSchema;
import com.github.fge.jsonschema.main.JsonSchemaFactory;
import com.github.fge.jsonschema.processors.syntax.SyntaxValidator;
import com.github.fge.jsonschema.report.ProcessingMessage;
import com.github.fge.jsonschema.report.ProcessingReport;

public abstract class AbstractJsonSchemaTest {

    public JsonSchema loadSchema(String filename) throws ProcessingException, IOException {
        JsonSchemaFactory factory = JsonSchemaFactory.byDefault();
        SyntaxValidator validator = factory.getSyntaxValidator();

        JsonNode node = loadJsonNode(filename);

        ProcessingReport report = validator.validateSchema(node);
        Assert.assertTrue("Schema is not valid!", report.isSuccess());

        JsonSchema schema = factory.getJsonSchema("resource:/" + filename);
        Assert.assertNotNull(schema);

        return schema;
    }

    public JsonNode loadJsonNode(String resourceName) throws IOException {
        String jsonString = loadJsonString(resourceName);

        ObjectMapper mapper = new ObjectMapper();
        JsonNode jcontent = mapper.readTree(jsonString);

        return jcontent;
    }

    public String loadJsonString(String resourceName) throws IOException {
        InputStream is = null;
        InputStreamReader isr = null;
        StringBuilder buff = new StringBuilder();

        try {
            is = Thread.currentThread().getContextClassLoader().getResourceAsStream(resourceName);
            isr = new InputStreamReader(is, Charset.defaultCharset());
            BufferedReader reader = new BufferedReader(isr);

            String line = null;
            while ((line = reader.readLine()) != null) {
                buff.append(line).append("\n");
            }

            reader.close();
            isr.close();
            is.close();
        } finally {
            if (isr != null) {
                isr.close();
            }
            if (is != null) {
                is.close();
            }
        }

        return buff.toString();
    }

    public void validateSchema(String schemaFilename) throws ProcessingException, IOException {
        loadSchema(schemaFilename);
    }

    public void runInvalidJsonTest(String schemaFilename, String filename) throws IOException, ProcessingException {
        JsonSchema schema = loadSchema(schemaFilename);

        JsonNode instance = loadJsonNode(filename);

        ProcessingReport report = schema.validate(instance);
        Assert.assertFalse("Expected validation to fail!", report.isSuccess());

    }

    public void runValidJsonTest(String schemaFilename, String filename) throws IOException, ProcessingException {
        JsonSchema schema = loadSchema(schemaFilename);

        JsonNode instance = loadJsonNode(filename);

        ProcessingReport report = schema.validate(instance);
        Iterator<ProcessingMessage> i =report.iterator();
        StringBuilder buff = new StringBuilder("Expected validation to succeed! Messages:\n");
        while (i != null && i.hasNext()) {
            ProcessingMessage pm = i.next();

            // attempting to pretty print the json
            ObjectMapper mapper = new ObjectMapper();
            String prettyPrintJson = mapper.writerWithDefaultPrettyPrinter().writeValueAsString(pm.asJson());

            buff.append(prettyPrintJson).append("\n\n");
        }
        Assert.assertTrue(buff.toString(), report.isSuccess());
    }

}
```

##Editors
Eclipse Json Editor Plugin  is a pretty basic eclipse plugin that does formatting, but doesn't do any syntax checking.  Also the formatting is very limited, you cannot change it other than to override the workspace default rule for white space.
Because JSON Schema is just a JSON document you can in theory simply use any json editor.  Some suggestions seen were to use a JavaScript editor.  But in practice this gets you very little.  For example, the Eclipse JavaScript editor does not reformat JSON though it should be able to.

##Java Object Generation
We are not doing object generation.  Updates on tips welcome!

##JSON Equality
When you need to do simple comparison of 2 json documents for equality (maybe after doing json2java then java2json) consider JSONAssert [12]. Can do non-strict JSON string comparisons (element order and extra fields ignored) very simply.  Strict comparison can be done as well, but that seems like a lot of extra work to get things in the same order again.

For maven dependency see the quickstart: http://jsonassert.skyscreamer.org/quickstart.html
```
@Test
public void jsonTest() throws Exception {
    String json1 = "{\"f1\":1,\"f2\":2}";
    String json2 = "{\"f2\":2,\"f1\":1}";
    JSONAssert.assertEquals(json1, json2, false);
}
```

#References
[1] json schema site - http://json-schema.org/<br/>
[2] json schema documentation - http://json-schema.org/documentation.html<br/>
[3] examples - http://json-schema.org/examples.html<br/>
[4] advanced examples - http://json-schema.org/example2.html<br/>
[5] User Group about JSon Schema - https://groups.google.com/forum/#!forum/json-schema<br/>
[6] StackOverflow tag for JSON Schema - https://stackoverflow.com/questions/tagged/jsonschema<br/>
[7] Another JSON schema documentation from spacetelescope - https://spacetelescope.github.io/understanding-json-schema<br/>
[8] Another JSON schema documentation from usingjsonschema - http://usingjsonschema.com/?page_id=5<br/>
[9] Another JSON schema documentation from jsonary - http://jsonary.com/documentation/json-schema/<br/>
[10] An example of tool to create and validate JSON schema online - http://jsonschema.net/<br/>
[11] validation - http://json-schema-validator.herokuapp.com/syntax.jsp<br/>
[12] JSONAssert - http://jsonassert.skyscreamer.org/<br/>





