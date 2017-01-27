# jsonschema
jsonschema is an implementation of JSON Schema for Python.

**Basic data types**

At its core, JSON Schema defines the following basic types:
  * String - string
  * Numeric types - number (integer, float)
  * Object - dict
  * Array - list
  * Boolean - bool
  * Null - none

**type:**

  * The type keyword may either be a string or an array:
  * If it’s a string, it is the name of one of the basic types above.
  * If it is an array, it must be an array of strings, where each string is the name of one of the basic types, and each element is unique. In this case, the JSON snippet is valid if it matches any of the given types.
  * Here is a simple example of using the type keyword with string value:

    __{ "type": "number" }__
    
      * 42 	- Valid
      * 42.0 	- Valid
      *	“42” 	- Invalid (This is not a number, it is a string containing a number.)
      
  * Here is a simple example of using the type keyword with array value:
  
    __{ "type": ["number", “string”] }__
    
     * 42 	- Valid
     * “42” 	- Valid
     * True - Invalid (bool is not allowed)

**required:**
  * The value of required keyword must be an array. This array must have at least one element. Elements of this array must be strings, and must be unique.
  * An object instance is valid against this keyword if its property set contains all elements in this keyword's array value.
 
  __{"type" : "object",
              "properties": {
                        "MRP": {"type" : "number"},
                        "Discount": {"type" : "number"}
	    },
	“required” : [“MRP”, “Discount”]
 }__
 
    * {“MRP” : 300, “Discount” : 100}	: Valid
    * {“MRP”: 300}	: Invalid ( Since discount is a required property)

**additionalProperties:**
  * The value of additionalProperties must be a boolean or a schema. 
  * If additionalProperties is true, validation succeeds even if there are extra properties present.
  
      __{"type" : "object",
                 "properties": {
                      "MRP": {"type" : "number"},
	                    "Discount": {"type" : "number"}
	                },
			          “additionalProperties” : True
	      }__
      
        * {“MRP” : 300, “Discount” : 100}	: Valid
        * {“MRP”: 300, “SP” : 200, “Discount” : 23} : Valid (Since additional property is allowed)

* If additionalProperties is false, validation succeeds only if the instance is an object and all properties on the instance were covered by "properties" and/or "patternProperties". This must be set if we want to strict schema validation.
     
     __{"type" : "object",
                 "properties": {
                      "MRP": {"type" : "number"},
                      "Discount": {"type" : "number"}
                 },
          		  “additionalProperties” : False
       }__
       
      *	{“MRP” : 300, “Discount” : 100}	: Valid
      *	{“MRP”: 300}	: Valid (required is not given)
      *	{“MRP”: 300, “SP” : 200} :	Invalid (Since additional property is not allowed)
      
* If additionalProperties is an object, validate the value as a schema to all of the properties that weren't validated by "properties" nor "patternProperties".

    __{"type" : "object",
                "properties": {
                        "MRP": {"type" : "number"},
                        "Discount": {"type" : "number"}
                 },
      			  “additionalProperties” : {"type" : "number"}
    	}__
      
      *	{“MRP” : 300, “Discount” : 100}	: Valid
      *	{“MRP”: 300, “SP” : 200} :	Valid (Since additional property is of number type)
      *	{“MRP”: 300, “Name” : “String”} : Invalid (Since additional property is of string type)
      
**Enumerated Values:** 
  * The enum keyword is used to restrict a value to a fixed set of values. It must be an array with at least one element, where each element is unique.
  * The following is an example for validating street light colors:
  
    __{ "type": "string",
       "enum": ["red", "amber", "green"]
      }__
      
      * “Red”	- Valid
      * “Blue”	- Invalid
      
**maxLength:**
  * The value of maxLength keyword must be an integer. This integer must be greater than, or equal to, 0.
  * A string instance is valid against this keyword if its length is less than, or equal to, the value of this keyword. The length of a string instance is defined as the number of its characters as defined by RFC7159.

    __{ “type” : “string”,
        “maxLength” : 5
      }__
      
      * “Super”	: Valid
      * “Curiosity”	: Invalid (length of the string > 5)
      
**minLength:**
  * A string instance is valid against minLength keyword if its length is greater than, or equal to, the value of this keyword.
  * The length of a string instance is defined as the number of its characters as defined by RFC7159.
  * The value of this keyword must be an integer. This integer must be greater than, or equal to, 0. minLength, if absent, may be considered as being present with integer value 0.

    __{ “type” : “string”,
        “minLength” : 5
      }__
      
      * “Super” : Valid
      * “bye” 	: Invalid (length of the string < 5)

**anyOf:**
  * the anyOf keyword is used to say that the given value may be valid against any (one or more) of the given subschemas.
  
    __{ "anyOf": [
        { "type": "string" },
        { "type": "number" },
        { "maxLength": 5 }]
      }__

      * 45 - Valid
      * “Girl” - Valid
      * True - Invalid
      
**allOf:**
  * To validate against allOf, the given data must be valid against all of the given subschemas.
  
    __{ "allOf": [
        { "type": "string" },
        { "maxLength": 5 }]
      }__
      
      * “Great” : Valid
      * “Intelligent” : Invalid (Length > 5)
      
**oneOf:**
  * To validate against oneOf, the given data must be valid against exactly one of the given subschemas.
  
    __{ "oneOf": [
        { "type": "number", "multipleOf": 5 },
        { "type": "number", "multipleOf": 3 }]
      }__
      
      * 9 : Valid
      * 15: Invalid (Cannot be multiple of both 3 and 5)
      
**not:**
  * The not keyword declares that a instance validates if it doesn’t validate against the given subschema.

    __{ "not": { "type": "string" } }__
        * 78 : Valid
        * “Great” : Invalid (It should not be a string)
        
**pattern:**
  * The pattern and Pattern Properties keywords use regular expressions to express constraints. 
  * The following example matches a simple North American telephone number with an optional area code:
    
    __{ "type": "string",
        "pattern": "^(\\([0-9]{3}\\))?[0-9]{3}-[0-9]{4}$"
      }__
      
      * "(888)555-1212"	: Valid
      * "(800)FLOWERS"	: Invalid (Allows only numbers)
      
**format:**
  * The format keyword allows for basic semantic validation on certain kinds of string values that are commonly used. 
  * Built-in formats:
      * The following is the list of formats specified in the JSON Schema specification.
        * hostname
        * ipv4	 
        * ipv6		- 	OS must have :func:`socket.inet_pton` function
        * email	 
        * uri	 	- 	requires rfc3987 
        * date-time	-	requires strict-rfc3339 
        * date	 	
        * time	 
        * regex	 
        * color	 	-	requires webcolors
  * The following example validates a string only if it satisfies date format.
  
        __"Birthdate" : { "type" : "string", "format":"date"}__
          
            * "1963-12-12" : Valid
            * “Birth”	: Invalid ( Though it is a string, it does not compile with date format)

**Validating a schema:**
  * The simplest way to validate an instance under a given schema is to use the validate function. 
  * When an invalid instance is encountered, a ValidationError will be raised or returned, depending on which method or function is used. 
  * In case an invalid schema itself is encountered, a SchemaError is raised.

  **Syntax:**
  
        validate(data, schema)
    
    If format has also to be checked, then 
    
        validate(data, schema, format_checker=FormatChecker())

__Example-1: (With enum)__

from jsonschema import validate
schema ={"type" : "object", 
            "properties" : {
          	     "itemcode" : {"type" : "number"},
                "name" : {"type" : "string"},
                "available" : {"type" : "boolean"},
                "itemqty": {
               		"type": "number",
                    		"enum" : [100,200,250,500,1000]
               },
                "pricelist" : {
                    "type" : "object",
                        "properties" : {
                            "100" : {"type" : "number"},
                            "200" : {"type" : "number"},
                            "250" : {"type" : "number"},
                            "500" : {"type" : "number"},
                            "1000" : {"type" : "number"},                        
                        },
                        "additionalProperties" : False                        
                },
                "brands" : {"type" : "array",
                    "items": {
                        "type": "string"
                    },
                },
            },
            "required": [ "itemcode", "name", "available", "itemqty","pricelist","brands"],   
            "additionalProperties": False            
        }


* test_data = {"itemcode" : 11, "name" : "Oil", "available" : True, "itemqty" : 200, "pricelist" : {"100" : 100,"200" : 200}, "brands" : ["SVS", "GoldWinner"]}        

    validate(test_data, schema) # The instance is valid.

* test_data = {"itemcode" : 11, "name" : "Oil", "available" : True, "itemqty" : 20, "pricelist" : {"100" : 100,"200" : 200},  "brands" : ["SVS", "GoldWinner"]}    

    validate(test_data, schema) #Raises validation error
jsonschema.exceptions.ValidationError: 20 is not one of [100, 200, 250, 500, 1000]
Failed validating 'enum' in schema['properties']['itemqty']:
    {'enum': [100, 200, 250, 500, 1000], 'type': 'number'}
On instance['itemqty']:
    20

__Example-2: (Same example extended With Format keyword)__

from jsonschema import validate
from jsonschema import FormatChecker
schema ={"type" : "array",
            "items" :{
                "type" : "object", 
                "properties" : {
                    "itemcode" : {"type" : "number"},
                    "name" : {"type" : "string"},
                    "available" : {"type" : "boolean"},
                    "itemqty": {
                        "type": "number",
                        "enum" : [100,200,250,500,1000]
                    },
                    "pricelist" : {
                        "type" : "object",
                            "properties" : {
                                "100" : {"type" : "number"},
                                "200" : {"type" : "number"},
                                "250" : {"type" : "number"},
                                "500" : {"type" : "number"},
                                "1000" : {"type" : "number"},              
                            },
                            "additionalProperties" : False                          
                    },
                    "brands" : {"type" : "array",
                        "items": {
                            "type": "string"
                        },
                    },
                    "purchased on" : {"type" : "string", "format" : "date"}
                },
                "required": [ "itemcode", "name", "available", "itemqty","pricelist","brands", "purchased on"],   
                "additionalProperties": False                
            }
        }

* test_data = [{"itemcode" : 11, "name" : "Oil", "available" : True, "itemqty" : 200, "pricelist" : {"100" : 100,"200" : 200}, "brands" : ["SVS", "GoldWinner"], "purchased on" : "2017-01-18"}]        
  
      validate(test_data, schema, format_checker=FormatChecker()) # This instance is valid

* test_data = [{"itemcode" : 11, "name" : "Oil", "available" : True, "itemqty" : 200, "pricelist" : {"100" : 100,"200" : 200}, "brands" : ["SVS", "GoldWinner"], "purchased on" : "2017-0118"}]

      validate(test_data, schema, format_checker=FormatChecker()) # This instance is invalid
  jsonschema.exceptions.ValidationError: '2017-0118' is not a 'date'
  Failed validating 'format' in schema['items']['properties']['purchased on']:
    {'format': 'date', 'type': 'string'}
  On instance[0]['purchased on']:
    '2017-0118'








