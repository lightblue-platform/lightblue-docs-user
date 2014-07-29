# JSON Style Guide

## Overview
The objective of this document is to define some general standards for the JSON and JSON Schema design but there are also some strict rules to keep a good quality and make the maintenance easier. Another style guide definition  that this article was inspired in [Google JSON Style Guide](https://google-styleguide.googlecode.com/svn/trunk/jsoncstyleguide.xml) which introduce many recommendations that they follow internally and follows the standards from http://www.json.org/ .
> Note: This article is a draft. The contents may change without any warranty

## The Standards

### Field/Property Name Format
 It is recommend to use the [camelCase](https://en.wikipedia.org/wiki/CamelCase) (instead of any other practices like [snake_case](https://en.wikipedia.org/wiki/Snake_case) ) as a single practice helps to improve the maintenance. Try to avoid name conflict putting meaningful details into the name (following the camelCase). We strongly suggest to use a letter as the first character, other characters might bring some doubts (specially for someone not familiar with JSON/JSON Schema, which might think that something that starts with `$` or `_` is a special property from JSON/JSON Schema). It is also recommended the arrays types should have plural names which makes more readable JSON/JSON Schema. The schema/object might use `description` to make a better documentation about it and help future maintainer.




