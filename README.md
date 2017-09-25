[![Build Status](https://travis-ci.org/delvelabs/marshmallow-autoschema.svg?branch=master)](https://travis-ci.org/delvelabs/marshmallow-autoschema)
[![PyPi](https://badge.fury.io/py/marshmallow-autoschema.svg)](https://badge.fury.io/py/marshmallow-autoschema)

# marshmallow-autoschema

Generate marshmallow schemas from code annotations and type hinting, converting
object structures to serializable native types and back effortlessly.


```python
from marshmallow_autoschema import autoschema, validate_field, Many, One
from marshmallow.validate import Range


@validate_field('word_count', Range(0, 10000))
@autoschema
class Page:

    def __init__(self, *,
                 word_count: int=0) -> None: pass


@autoschema
class Book:

    def __init__(self, *,
                 cover: One[Page],
                 pages: Many[Page]) -> None: pass

data = {
    "cover": {"word_count": 12},
    "pages": [
        {"word_count": 0},
        {"word_count": 12},
        {"word_count": 100},
    ],
}

book, errors = Book.load(data)

assert isinstance(book, Book)
assert isinstance(book.cover, Page)

out, errors = book.dump()

assert data == out
```

For the decorator to work properly, a few properties are required:

* All properties must be listed as arguments in the constructor.
* Only kwonly arguments are considered, the asterisk is required in the declaration.
* Type annotations are required.

Additionally:

* Default values can be used and apply as normal normal function default values.
* On a List[] type, =None will default to an empty list.
* The constructor body can be specified and is called after all members have been assigned.

## Advanced Usage

@autoschema and @autoschema_camelcase are offered as a convenience for the most common cases.
For other cases, schema_metafactory can be used.

Arguments:

* *schema_base_class*: As a minimum, the base schema class must have a *post_load* handler to create the model object.
* *field_namer*: Function to convert the class members into the serialized name.
* *extended_field_map*: Include additional field type handlers for your definitions.


## Additional Examples

* [marshmallow-har](https://github.com/delvelabs/marshmallow-har)

## License

Copyright (c) 2017- Delve Labs Inc.

This library is distributed under the MIT License. See joined LICENSE file for details.
