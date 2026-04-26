# value_object - a JavaScript-like Prototypal Object in Smallworld GIS Magik

In this [blog post](https://markhing.com/smallworld/beeble-object-a-javascript-like-prototypal-object/) Mark Hing proposed an object that uses prototypal inheritance instead of class inheritance and called it beeble_object (yes, he is a Douglas Adams fan).

He uses this object in other [blog posts](https://markhing.com/?s=beeble_object) to show the power of functional programming and other concepts yet unknown to or rarely used in Magik.

I encourage you to read the posts and give value_object a chance to enhance your way of Magik programming.

Mark was so kind to allow me to publish it with a more technical name.

# value_object_benchmark

This is a benchmark you can easily adapt to your specific needs. I, for example, wanted to know the impact of creating value_objects from rwo_records.

## Scenarios

I compared three data types:
1.	rwo_record
2.	value_object
3.	property_list
 
Every data type has access to these fields
•	Id
•	material (physical field, string)
•	diameter (physical field, string)
•	risk (physical field, float)
•	pipe (geometry_field, chain)
•	length (physical field, int, unit_value)
•	count_defects (derived field, int)
 
These are used in the procedures IS_RELEVANT?, HAS_RISK?, CALC_DIFFERENCE.
 
The procedures EXTRACT_VALUE_OBJECT and EXTRACT_PROPERTY_LIST transform the rwo_records into the other data types.
 
The procedure MEASURE_TIME invokes a given procedure that imports the above mentioned procedures (Refer to the code for implementation details.).

## Results 
The first run was a disappointment since value_object and property_list are a lot slower than rwo_record. Then I added the measurement for only the extraction of value_object and for a minimal extraction of value_object.
 
The final results are:
 
Magik> value_object_benchmark()
Count :203832

**Scenario A: rwo_record** 
 
Checks if a rwo_record is relevant and then transforms it into a value_object.

Time A: 300000 ms

**Scenario B: value_object** 
 
Maps rwo_records to value_objects and transforms these, if relevant, into new value_objects with less fields.

Time B: 814000 ms

**Scenario C: property_list**
 
Maps rwo_records to property_lists and transforms these, if relevant, into value_objects.

Time C: 801000 ms

**Scenario D: Only value object extraction**

Just map rwo_records to value_objects.

Only value object extraction: 722000 ms

**Scenario E: Only value object with logical method extraction**
 
Just map rwo_records to value_objects. Considers only the logical method.

Only value object with logical method extraction: 589000 ms

Scenario E reveals the logical method as the bottleneck of the extraction, as it alone accounts for 589,000 ms out of the 722,000 ms measured in Scenario D (~82 %).

## When to use value_object

Despite the performance overhead, value_object offers advantages in specific scenarios:

- **Serialization:** Unlike rwo_record, which is tightly coupled to the 
  Smallworld database (Active Record pattern), a value_object is a plain 
  data object that can be serialized to JSON — making it well-suited for 
  web services and external integrations.
- **Reduced coupling:** You can extract only the fields relevant to a 
  downstream process, keeping your logic lean and focused.
- **Testability:** Since value_object has no database dependency, it can 
  be instantiated directly in unit tests — no live database required.

## Conclusion

Consider carefully in which scenarios value_object may have advantages over using rwo_record directly. Consider, too, what kind of information the value_object really needs for further processes.

And check if your logical methods need a refactoring to solve eventual performance issues.
