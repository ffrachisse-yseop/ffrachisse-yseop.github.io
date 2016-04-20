Yseop Savvy API
==============

**Yseop Savvy API** provides some tools to understand graphs, diagrams and charts, by generating clear and intelligible descriptions. Those texts are not technical, they simply highlight the major graph variations, without trying to speculate on the reasons for these changes. This API is mainly meant for BI purposes but can be used in various sectors, as it is flexible and not semantically bound to a specific domain.

 For now, it already has been integrated in **Yseop Savvy for Qlik** (plugin for Qlik Sense Desktop), and **Yseop Savvy for Microsoft Excel** (add-in for Microsoft Office 2007 and further).
 
----------


General Functioning
-------------

{:#generalfunc}
Yseop Savvy API is a **REST API**([What is REST API?] (http://www.restapitutorial.com/lessons/whatisrest.html)). You can process some tests using the Google Chrome Extension **Postman** (see more [here](https://www.getpostman.com/)).

**Our API is available at:** https://savvy-api.yseop-cloud.com/sandbox/api/v1/describe-chart


Header Parameters
-------------

### Security

The API needs a **Basic Authentication**. The *login* is the JWT token you've been given on [Yseop Savvy's Website](https://savvy.yseop.com/download/). If you don't have any yet, please go get one. The *password* must stay empty.

```
 Authorisation="Basic myKey:"
```

### Input Type

You need to indicate the input format, which is always *json*:
```
 Content-Type="application/json;charset=UTF-8"
```

### Output Format

The output format has to be set as *html*:
```
 Accept="text/html"
```

Chart Description
-------------

#### Objects

#####  ROOT OBJECT

Top-level object that gives all the information necessary to the application. There can be several charts descriptions, but one is mandatory.

Field | Type| Mandatory| Description
-------- | ---| ------| ------
extensionVersion| String| Yes | Version Number of the input.
charts| [[ChartObject](#chart-object)]| Yes | List of charts to describe.

```
{
    "extensionVersion": "2",
    "charts": [ ... ]
}
```

##### CHART OBJECT

Chart Information about dimensions, measures and facts.

Field | Type| Mandatory| Description
-------- | ---| ------| ------
inputChart| [InputChartObject](#input-chart-object)| Yes| General information on the Chart.
outputText| [OutputParamObject](#output-param-object)| Yes| Meta-data for preferences relating to the output text.
dimensions| [[DimensionInfoObject](#dimension-info-object)]| Yes| There must be one Dimension block per dimension in the chart, 2 max.
measures| [[MeasureInfoObject](#measure-info-object)]| Yes| There must be one Measure block per measure in the chart, 2 max.
facts| [[FactObject](#fact-object)]| Yes| List of facts, representing the measures values for each dimension (or couple of dimensions).

```
{
	"inputChart": 
	{
		"type": "LINE_CHART",
		"multiSeriesType": "UNRELATED"
	},
	"outputText": 
	{ 
		"levelOfDetail":7,
		"lang":"EN" 
	},
	"dimensions": 
	[{ 
		"label": "serie",
		"cardinal": 3 
	}],	
	"measures": 
	[{ 
		"label": "sales",
                "defaultValue": "0",
                "meaningOfUp": "GOOD"
	}],	
	"facts": [...]	
}
```


##### INPUT CHART OBJECT

Semantic information about the graph. Those meta-data are used to understand the relations between data.

Field | Type| Mandatory| Description
-------- | ---| ------| ------
type| [ChartTypeEnum](#chart-type-enum)| Yes| Format of the chart.
multiSeriesType| [[MultiSeriesEnum](#multi-series-enum)]| Yes| How the data are related to each other.
mainDimensionIndex| Integer| No| The index of the dimension which is considered as *main* in the analysis. Only used when there are several dimensions.
mainDimensionOrdered| Boolean| No| **Deprecated**. Use the property  *ordered* of each dimension description.
disableInterpolation| Boolean | No| When data values are missing in a chart, the generator tries to compute approximate ones. If interpolation is disabled, the default value is used for those missing values.
currentSelection| [[SelectionObject](#fact-object)]| No| The selection tag is optional, it is used to describe the current selection (on other dimensions than the one of the data). Eg: The commented graph display the saled by month, but only for the United Kingdom.
otherPartPresents| Boolean| No| Indicates if low values should be grouped together to form a section *Others*.
singularExceptions| [String]| No| Yseop identifies if your measures and dimensions are singular or plural. However, you can override this identification by entering here the nouns that should be singular.
pluralExceptions| [String]| No| Same as for *singularExceptions*, but for plural entities.

```
{
	"type": "LINE_CHART",
	"mainDimensionIndex": 0,
	"multiSeriesType": "UNRELATED"
}
```

##### OUTPUT PARAM OBJECT

Information about the expected output text.

Field | Type| Mandatory| Description
-------- | ---| ------| ------
levelOfDetail| Integer| Yes| Number between *1* and *7*, where 1 produces the shortest text, and 7 the most verbose one.
lang| [LangEnum](#lang-enum)| Yes| Language of the generated text.

```
{ 
    "levelOfDetail": 7,
    "lang": "EN" 
}
```

##### DIMENSION INFO OBJECT

This object describes one of the dimensions of the graph. It is a part of the graph definition [here](#chart-object).

Dimensions are the basis on which all calculations are performed.They are descriptive attributes, e.g. text fields, dates or discrete numbers. There can be several dimensions if graphs aggregate several calculations.


Field | Type| Mandatory| Description
-------- | ---| ------| ------
label| String | Yes| The Dimension's name (can contain spaces).
technicalLabel | Boolean | No | Set to true if the *label* is technical (eg: *=SUM({sales}, Month)*) rather than textual (eg: *Sales*).
othersLabel| String| No| Sometimes data are grouped in a section 'Other' in graphs. You can define here a specific label for this section.
cardinal| Integer| Yes| Number of elements in the Dimension.
tags | [String] | No | Optional, it can be used to determine if the dimension is ordered or unordered. Currently recognized values: *\$numeric*, *\$integer*, *\$timestamp*, *\$date*.
ordered | Boolean | No | Overrides automatic detection (via tags or analysis of dimension points) if set to *true* or *false*. Do not set this attribute if automatic ordering is desired.

```
{ 
	"label": "month",
	"othersLabel": "Autres",
	"cardinal": 12 
}
```

##### MEASURE INFO OBJECT

This object describes one of the measures of the graph. It is a part of the graph definition [here](#chart-object).

Measures are the data computed for each dimension. They represent what will be visible in the chart.

Field | Type| Mandatory| Description
-------- | ---| ------| ------
label| String | Yes| The Dimension's name (can contain spaces).
technicalLabel| Boolean| No| Sometimes data are grouped in a section 'Other' in graphs. You can define here a specific label for this section.
defaultValue| String| Yes| Number of elements in the Dimension.
meaningOfUp| [MeaningOfUpEnum](#meaning-of-up-enum)| Yes| Sometimes data are grouped in a section 'Other' in graphs. You can define here a specific label for this section.
unit| String| No| Defines the unit for currencies or quantities.

```
{
	"label": "sales",
	"technicalLabel": false,
	"defaultValue": "0",
	"meaningOfUp": "GOOD",
	"unit": "$" 
}
```

##### FACT OBJECT

The Facts represent all the points of the graph: it is a matrix between the different dimensions, where values are measures.

Field | Type| Mandatory| Description
-------- | ---| ------| ------
dimensions| [[MeasureValueObject](#measure-value-object)]| Yes| Values for each dimension.
measures|  [[DimensionValueObject](#dimension-value-object)]| Yes| Values for each measure.

```
{ 
	"dimensions":[
			{ "index": 0, "label": "01/01/2015", "position": 0 },
			{ "index": 1, "label": "United Kingdom", "position": 0 }
		     ],
	"measures":[
			{ "index": 0, "value": "150" }
		   ] 
}
```

##### DIMENSION VALUE OBJECT

For each point, specify the value of one of the dimensions (used in Fact declaration, [here](#fact-object)).

Field | Type| Mandatory| Description
-------- | ---| ------| ------
index| Integer| Yes| Index of the dimension in the 'Dimensions' array (indexes start at 0).
label|  String| Yes | Used in text generation for identifying this point.
position| Integer| Yes| The position is the 'coordinate' of the dimension point related to other points on this dimension. For instance January is the first month. Only relevant for ordered dimensions.

```
{ 
	"index": 0, 
	"label": "01/01/2015", 
	"position": 0 
}
```

##### MEASURE VALUE OBJECT

For each point, specify the value of one of the measures (used in Fact declaration, [here](#fact-object)).

Field | Type| Mandatory| Description
-------- | ---| ------| ------
index| Integer| Yes | Index of the measure in the 'Measures' array (indexes start at 0).
value|  String | Yes | The value for this measure (only numeric values are accepted).

```
{
	"index": 0, 
	"value": "150"
}
```

##### SELECTION OBJECT

This object is used to describe the current selection (on other dimensions than the one of the data). Eg: the commented graph display the saled by month, but only for the United Kingdom.

Note: It is not necessary to indicate selections for dimension(s) of the chart. Actually, they will be ignored.

Field | Type| Mandatory| Description
-------- | ---| ------| ------
field| Integer| Yes| Name of the dimension/measure to which a selection is applied.
values|  [String]| Yes | Values selected in *field* dimension/measure. Currently, only the 5 first values are used by the knowledge base.
selectedElementCount| Integer| Yes| The number of selected elements can differ from the number of elements of the *values* array.

```
{                
	"field": "Region",
	"values": ["United Kingdom", "France"],
	"selectedElementCount": 15 
}
```

#### Enums

##### CHART TYPE ENUM

Currently, there are three main types of charts handled by the application. However, some more complicated graphs (like points charts) can fit in one of those categories.

Value| Description
---- | ------
LINE_CHART| Use to describe line or points charts.
BAR_CHART| Use to describe bar or cluster charts.
PIE_CHART| Use to describe pie charts.


##### MULTI SERIES ENUM

Indicates if the measures are related to each other. For *value vs reference*, the value should be first and the reference second in the measure order.

Value| Description
---- | ------
UNRELATED| Measures are not related to each other.
VALUE_VS_REFERENCE| The second measure is supposed to be relative to the first one.

##### LANG ENUM

Output language for generated text. For now, only English is handled.

Value| Description
---- | ------
EN| English

##### MEANING OF UP ENUM

Semantic information about the measures values. Specifies if high values should be interpreted with a positive or negative meaning.

Value| Description
---- | ------
GOOD| Means that high values are positive information (eg: sales).
BAD| Means that high values are negative information (eg: expenses).

Output Format
-------------

When all went well, the Serveur return the code **200**:

Code| Returns| Description
---- | ------ | ------
200| HTML | Success.

Here is an example of returned output:

```
<html xmlns:y="http://www.yseop.com/engine/3" 
      xmlns:yt="http://www.yseop.com/text/2" 
      xmlns:yp="http://www.yseop.com/profiling/1">
    <body>
        <div id="yseop-text">
            <div class="yseop-result">
This chart shows the Revenue for the following Product Sub Group Descs: Fresh Vegetables, Hot Dogs, Fresh Fruit, Frozen Vegetables, Sugar, Bologna, Coffee and 63 others.
<p></p><p></p>
What stands out in this situation is that a few Product Sub Group Descs account for
more than half of the total. There is obviously a dominant group of Product Sub Group Descs. This group is composed of six Product Sub Group Descs: Fresh Vegetables with
11.57 %, Hot Dogs with 9.51 %, Fresh Fruit with 8.60 %, Frozen Vegetables with 7.50
%, Sugar with 7.01 % and Bologna with 6.09 %.
<p></p><p></p>
Combined, the 64 other Product Sub Group Descs account for the rest of the list, accounting for 49.72 % of the total.
<p></p>
When taken together, the 70 Product Sub Group Descs amount to a total value of 67,119,290. The average is 958,847.
<p></p>
		</div>
		<span class="errors-and-warnings"></span>
	</div>
	<div class="technical-info">
        <div id="engineErrors"> Engine errors :<br></div>
        <div> Engine process time: <span id="processingTime">132.96</span>ms</div>
        <div> Timestamp: <span id="timestamp">2016-04-11T08:05:20:209</span></div>
        <div> Engine PID: <span id="enginePID">71D6</span></div>
        <div> KB Version: <span id="kbVersion">1.0.9</span></div>
    </div>
</div>
</body>
</html>
```


 Error Codes
-------------

Here is a list of all the error messages you can receive if the text generation failed:

### 400: Bad Request

Code| Returns | Description
---- | ------ | ------
400| JSON| Generally means the json format is not compliant to the schema. The body contains the error message.

```
[
  {
    "level": "error",
    "schema": {
      "loadingURI": "#",
      "pointer": "/properties/charts/items/properties/inputChart"
    },
    "instance": {
      "pointer": "/charts/0/inputChart"
    },
    "domain": "validation",
    "keyword": "type",
    "message": "instance type (string) does not match any allowed primitive type (allowed: [\"object\"])",
    "found": "string",
    "expected": [
      "object"
    ]
  }
]
```

### 401: Unauthorized
  
Code| Returns | Description
---- | ------ | ------
401| HTML| Means your JWT token is not correct or expired.

```
<html>
    <head>
        <title>Apache Tomcat/7.0.52 (Ubuntu) - Rapport d''erreur</title>
        <style> ...</style> 
    </head>
    <body>
        <h1>Etat HTTP 401 - </h1>
        <HR size="1" noshade="noshade">
        <p>
            <b>type</b> Rapport d''état
        </p>
        <p>
            <b>message</b>
            <u></u>
        </p>
        <p>
            <b>description</b>
            <u>La requête nécessite une authentification HTTP.</u>
        </p>
        <HR size="1" noshade="noshade">
        <h3>Apache Tomcat/7.0.52 (Ubuntu)</h3>
    </body>
</html>
```


### 404: Not Found
  
Code| Returns | Description
---- | ------ | ------
404| HTML| The application does not exist or there is a mistake in the URL.

```
<html>
    <head>
        <title>Apache Tomcat/7.0.52 (Ubuntu) - Rapport d''erreur</title>
        <style>...</style> 
    </head>
    <body>
        <h1>Etat HTTP 404 - </h1>
        <HR size="1" noshade="noshade">
        <p>
            <b>type</b> Rapport d''état
        </p>
        <p>
            <b>message</b>
            <u></u>
        </p>
        <p>
            <b>description</b>
            <u>La ressource demandée n''est pas disponible.</u>
        </p>
        <HR size="1" noshade="noshade">
        <h3>Apache Tomcat/7.0.52 (Ubuntu)</h3>
    </body>
</html>
```

### 500: Server Error
  
Code| Returns | Description
---- | ------ | ------
500| XML| Can mean the json is misformed, or an exception occured in the application.

```
<?xml version="1.0" encoding="ISO-8859-1"?>
<y:output xmlns:y="http://www.yseop.com/engine/3">
    <y:data-end>
        <y:logging>
            <y:log level="error">
                <y:message>
	                JsonParseException Unexpected close marker ']': 
	                expected '}' (starting at ... line: 1, column: 2434])
	                at ... line: 1, column: 2473] 
		        </y:message>
                <y:source name="yseop-manager"></y:source>
                <y:owner name="yseop-manager"></y:owner>
            </y:log>
        </y:logging>
    </y:data-end>
</y:output>
```

### 503: Not Available
  
Code| Returns | Description
---- | ------ | ------
503| HTML| The server is currently unable to respond, because an update is running. Can occur too if the license is expired.


Complete Example
-------------

```
{
    "extensionVersion": "2",
    "charts": 
		[{ 
			"inputChart": 
			{ 
				"type": "LINE_CHART",
				"mainDimensionIndex": 0,
				"mainDimensionOrdered": true,
				"multiSeriesType": "UNRELATED",
				"disableInterpolation": true,
				"currentSelection": 
				[{                
					"field": "Region",
					"values": ["United Kingdom", "France"],
					"selectedElementCount": 15 
				}] 
			},
            "outputText": 
			{ 
				"levelOfDetail":7,
                "persona":"MANAGER",
                "lang":"EN" 
			},
				
            "dimensions": 
			[{ 
				"label": "month",
				"othersLabel": "Autres",
				"cardinal": 24 
			}],
				
            "measures": 
			[{ 
				"label": "number of laser swords on Earth",
                "technicalLabel": false,
                "defaultValue": "0",
                "meaningOfUp": "GOOD",
                "unit":"" 
			}],
			
			"facts": 
			[{ 
				"dimensions":[{ "index":0, "label":"01/01/2015", "position":0 }],
				"measures":[{ "index":0, "value":"150" }] 
			},
			{ 
				"dimensions":[{ "index":0, "label":"01/02/2015", "position":1 }],
				"measures":[{ "index":0, "value":"124.5"}] 
			},
			{ 
				"dimensions":[{ "index":0, "label":"01/03/2015", "position":2 }],
				"measures":[{ "index":0, "value":"150.64499999999998" }] 
			},
			{ 
				"dimensions":[{ "index":0, "label":"01/03/2015", "position":3 }],
				"measures":[{ "index":0, "value":"150.64499999999998" }]
			},
			{ 
				"dimensions":[{ "index":0, "label":"01/04/2015", "position":4 }],
				"measures":[{ "index":0, "value":"150.64499999999998" }] 
			},
			{ 
				"dimensions":[{ "index":0, "label":"01/05/2015", "position":5 }],
				"measures":[{ "index":0, "value":"150.64499999999998" }] 
			},
			{ 
				"dimensions":[{ "index":0, "label":"01/06/2015", "position":6 }],
				"measures":[{ "index":0, "value":"150.64499999999998" }] 
			},
			{ 
				"dimensions":[{ "index":0, "label":"01/07/2015", "position":7 }],
				"measures":[{ "index":0, "value":"54" }] 
			},
			{ 
				"dimensions":[{ "index":0, "label":"01/08/2015", "position":8 }],
				"measures":[{ "index":0, "value":"150.64499999999998" }]
			},
			{ 
				"dimensions":[{ "index":0, "label":"01/09/2015", "position":9 }],
				"measures":[{ "index":0, "value":"40" }] 
			},
			{ 
				"dimensions":[{ "index":0, "label":"01/10/2015", "position":10 }],
				"measures":[{ "index":0, "value":"30" }] 
			},
			{ 
				"dimensions":[{ "index":0, "label":"01/11/2015", "position":11 }],
				"measures":[{ "index":0, "value":"20" }] 
			},
			{ 
				"dimensions":[{ "index":0, "label":"01/12/2015", "position":12 }],
				"measures":[{ "index":0, "value":"1" }] 
			}] 
		}]
}

```


Support
-------------

If you encounter any trouble using this API, please visit our [FAQ page](https://savvy.yseop.com/support/faq/) or the [Q&A section](https://savvy.yseop.com/support/questions-and-answers/).

See Also
-------------

TODO: link to the swagger page.



