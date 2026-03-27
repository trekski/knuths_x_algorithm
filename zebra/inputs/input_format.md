# Input Format

This document describes the format of the input YAML files used in the zebra puzzle solver and how the object described by that file is reshaped before being used to costruct the sparse matrix

on the highest level each YAML file describes a list of dictionaries. Each such dictionary represents one constraint and its keys and values the parameters of that constraint (type of the constraint, used dimensions and their values)

## Encoding of "identity" Constrains

An identity constraint is one that is in the form:

> Person with `Attrribute A` also has `Attrribute B`

This is encoded in the file as a dictionary with two entries. The key being the dimension name and the value being the dimension value.

> ```yaml
> - ...
> - first_attribute_dimension_name : first_attribute_value
>   second_attribute_dimension_name : second_attribute_value
> - ...
> ```

After ingestion, the constraint is dictionary is rewritten as a named tuple:

```python
Condition(
    type = "_is", # fixed value. redundant here, but kept to be easier to align with following data strucutres
    params = [ # here order does not matter, but we keep it a list
        Attribute(
            dim = first_attribute_dimension_name,
            val = first_attribute_value 
        ),
        Attribute(
            dim = second_attribute_dimension_name,
            val = second_attribute_value 
        )
    ]
)
```

For example the constraint 

> The `Spaniard` [`nationality`] owns the `dog` [`pet`]

Would be encoded as 

> ```yaml
> - ...
> - nationality : Spanish
>   pet : dog
> - ...
> ```

and represented as a following data structure after fiel is parsed:

```python
Condiition(
    type = "_is", 
    params = [
        Attribute(dim='nationality', val='Spanish'),
        Attribute(dim='pet',  val='dog')
    ]
)
```

## Encoding of "Neighbor" Constrains

A  "neighbor" constraint is one that is in the form:

> Person with `Attrribute A` lives nextot/left of/right of person with `Attrribute B`

This is encoded in the file as a dictionary with one entry entries. The key describes the type of the neighbor cosntraint

* `_left` - in case the person described by firt attribute on the list lives left (house number **less by 1**) of the person described by the second attribute
* `_left` - in case the person described by firt attribute on the list lives right (house number **increased by 1**) of the person described by the second attribute
* `_adj` (adjacent) - in case the two menioned persons live next to each other, but we don't knwo who lives on which side.

The value assigned to that key is a nested list of dictionaries. Each item on that list represents an attribute of one of the neighbors. The items (dictionaries) eahc have one entry, where the key is the attribute's dimension name and the value is the attribute value.

Example:

> The `green` [`coloured'] house is immediately to *`the right`* of the house whoe owner `drink` `tea`.

would be encoded as 

> ```yaml
> - ...
> - _right:
>   - color: green
>   - drink: tea
> - ...
> ```

and represented as a following data structure after input is parsed:

```python
Condition(
    type = "_right",
    params = [    
        Attribute(dim ='color', val = 'green'),
        Attribute(dim = 'drink', val ='tea')
    ]
)
```

## Additional diemnsions and values

Most often the complete list of dimension vlaues in a zebra puzzle can be determined only when we also parse the question(s) at the end of the puzzle.

The encoding is very similar to the "neighbor" cosntraint with hte difference that:
- the top level key is "_question"
- the value under that key is a list of arbitrarily many items (nested dictionaries)

So the questions

> Now, who `drinks` `water`? Who owns the [`pet`] `zebra`?

woudl be encoded as:

```yaml
- ...
- _question:
  - drink : water
  - pet : zebra
- ...
```

and represented as a following data structure after parsing:

```python
Condition(
    type = "_question", 
    params = [
        Attribute('drink', 'water'),
        Attribute('pet', 'zebra')
    ]
)
```