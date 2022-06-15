---
title: "SondeHub Site Suggestions"
subtitle: "Processing site suggestions with Google Forms & GitHub"
date: 2022-05-24 00:00:00 -0400
categories: [SondeHub]
---

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/swagger-ui/4.11.1/swagger-ui.css">
<script type="text/javascript" language="javascript" src="https://cdnjs.cloudflare.com/ajax/libs/swagger-ui/4.11.1/swagger-ui-bundle.js"></script>

This blog post details how I created an automated system to collect user suggestions.

<h2>SondeHub Tracker</h2>

The SondeHub Tracker is an online website where you can view live weather baloon launches across the world.

The Tracker relies on the SondeHub database which consists of over 800 ground stations contributing live flight information to the database.

This data is processed and shown to users on the SondeHub tracker enabling recovery of radiosondes and basic weather predictions.

<h3>SondeHub Sites API</h3>

The majority of radiosondes tracked are launched on a regular schedule from a fixed location by national meterological services.

The SondeHub database includes a crowd-sourced database of known radiosonde launch sites along with information such as launch times, radiosonde types, and flight parameters.

This data can be used to generate real-time flight predictions and group flights for historical analysis.

The SondeHub Sites API returns this list of all known radiosonde launch sites which is then used in the SondeHub Tracker.

```
https://api.v2.sondehub.org/sites
```

```json
"94672": {
   "station": "94672",
   "station_name": "Adelaide Airport (Australia)",
   "position":[
      138.5203,
      -34.9525
   ],
   "alt": 6,
   "rs_types":[
      "41"
   ],
   "times":[
      "0:12:00",
      "0:00:00"
   ],
   "ascent_rate": 4.3,
   "burst_altitude": 27000
}   
```

The specific Python code and Elasticsearch Query for generating the API response can be found <a href="https://github.com/projecthorus/sondehub-infra/blob/a70f7aac4c3b4745a1894d9c7a261830ea982fa3/lambda/query/__init__.py#L343" target="_blank">here</a>.

The following Swagger UI component can be used to access the SondeHub Sites API and get real results.

<div id="OpenAPI"></div>

<style>
   .swagger-ui .wrapper {
      padding: 0px!important;
   }
   .swagger-ui .wrapper .col-12 {
      padding: 0px!important;
   }
   .swagger-ui .opblock .opblock-summary-path {
      max-width: 100%!important;
   }
   @media (max-width: 768px) {
      .swagger-ui .opblock-body select {
         min-width: 40%!important;
      }
   }
   .swagger-ui a.nostyle, .swagger-ui a.nostyle:visited, .swagger-ui .responses-inner h4, .swagger-ui .responses-inner h5, .swagger-ui .opblock .opblock-section-header h4, .swagger-ui .opblock .opblock-section-header>label {
      color: var(--heading-color)!important;
   }
   .swagger-ui .opblock-description-wrapper p, .swagger-ui .opblock-external-docs-wrapper p, .swagger-ui .opblock-title_normal p, .swagger-ui table thead tr td, .swagger-ui table thead tr th, .swagger-ui .opblock .opblock-summary-description, .swagger-ui .response-col_status, .swagger-ui .markdown p, .swagger-ui .btn, .swagger-ui .parameter__name, .swagger-ui .parameter__type, .swagger-ui .parameter__extension, .swagger-ui .parameter__in {
      color: var(--text-color)!important;
   }
   .swagger-ui .opblock .opblock-section-header {
      background-color: var(--btn-box-shadow)!important;
   }
</style>

<script>
   const paths = {
      "/sites": {
         "get": {
            "tags": [
               "SondeHub Sites"
            ],
            "summary": "Get launch sites",
            "description": "Use this to get the list of known launch sites and parameters",
            "parameters": [
               {
                  "in": "query",
                  "name": "station",
                  "type": "number",
                  "description": "Launch site number"
               }
            ],
            "responses": {
               "200": {
                  "description": "Returns a dictionary of launch sites"
               },
            },
         }
      }
   };

   const spec = {
      'swagger': '2.0',
      'paths': paths,
      'host': 'api.v2.sondehub.org'
   };

   SwaggerUIBundle({
      spec: spec,
      domNode: document.querySelector('#OpenAPI')
   })
</script>

The SondeHub Sites API uses a simple data structure for each station with mandator fields for location, name, id, and sonde type in addition to numerous optional fields.

These extra fields can include data such as the launch schedule, frequency that the radiosondes transmit on, specific notes, and flight parameters for ascent, burst, descent.

The API can either return a single station given it's ID or the list of all known stations which is used in the Tracker to display all launch sites on the map.

The API is also used internally to lookup flights parameters for any sonde that originates from that launch site and to archive flights after their completion.

<h2>Site Suggestions</h2>

The SondeHub Tracker includes an integrated modal that allows users to suggest changes to any launch site on the map.

This modal contains a customised hyperlink containing all the existing information for the launch site so that only the changes need to be supplied.

<h2>Google Forms</h2>

The original plan was to only use GitHub issues for all site suggestions however after trialing this approach it seemed that respondents were often confused.

The GitHub API allows for customised APIs that will automatically pre-fill the text and title fields for a new issue however this functionality is not supported for individual comments on an existing issue.

This limitation forced the use of Google Forms as it supported all form fields being prefilled from the URL.

<h3>Form Fields</h3>

The main consideration is designed the Google Form was ensuring that the correct data was collected in a suitable format.

This was achieved by splitting the form into three sections where the first contained the common fields mandatory for all stations.

The second section contained all the optional fields that might not yet exist for any particular station while the last section allowed users to submit any extra notes.

<h3>Field Validity Checks</h3>

Google Forms supports adding a custom regex check to each field that allows for extreme control over what inputs can be submitted.

The checks for certain fields such as ID, altitude, and flight parameters were all created with simple number checks.

The station name field required a custom regex to ensure that the correct naming structure was adhered with the country placed in brackets at the end.

<img src="{{"/assets/name_regex.svg" | relative_url }}" style="display:block;margin:auto;max-width:100%;"/>

The station coordinates field required a significantly more complex regex sequence that I borrowed from Stack Overflow which ensures the value is generally valid.

<img src="{{"/assets/coordinates_regex.svg" | relative_url }}" style="display:block;margin:auto;max-width:100%;"/>

<h3>Field Autofill</h3>

The Google Form assigns a unique ID to every field in the form that can be used to generate the URL used in the Tracker that automatically fills existing fields.

The ID for each field could only be determinned once the Form had been published but does appear to stay constant even after minor modifications to the form.

The ID can be determined by visting the form and using developer tools to select each field and find the second 9 digit numerical value of `[data-params]`.

```
data-params="%.@.[564009759,"Station ID",null,0,[[749833526,[],true,[],[[1,10,[],"5 digit WMO code e.g. 94672"]],null,null,null,null,null,[null,[]]]],null,null,null,[],null,null,[null,"Station ID"]],"i17","i18","i19",false]"
```

The fields can also be determinned using the Get pre-filled link integrated in the Google Forms creation page

Once the ID for each field was recordered a custom URL could be constructed.

The value for each field that will be pre-filled needs to be appended to the end of the URL with the following format

```
entry.796606853=Modify+Existing+Site
```

```
entry.749833526=94672
```

The complete URL for the launch site at Adelaide Airport contains most of the available fields.

```
https://docs.google.com/forms/d/e/1FAIpQLSfIbBSQMZOXpNE4VpK4BqUbKDPCWCDgU9QxYgmhh-JD-JGSsQ/viewform?usp=pp_url&entry.796606853=Modify+Existing+Site&entry.749833526=94672&entry.675505431=Adelaide+Airport+(Australia)&entry.1613779787=-34.9525,138.5203&entry.753148337=6&entry.509146334=4.3&entry.1897602989=27000&entry.267462486=6.4
```

This pre-fill functionality isn't easily implemented for the sondes type and launch times fields so currently these must be manually entered each time.

<h2>Google Apps Script</h2>

The Google Form will automatically add the responses to the form to a Google Sheets where Google Apps Script can be setup to automatically run.

Google Apps Script is a JavaScript based scripting platform that enables easy integration and automation of tasks with Google applications such as Sheets.

When a new launch site suggestion is received a custom Apps Script is executed to process the suggestion and publish it to GitHub as a new comment on the main issue.

<h3>Processing Fields</h3>

The script begins by getting the value for each provided field and formatting into the correct data type.

This involves generating lists for coordinates, launch times, and sonde types in addition to reading flight parameters as floats.

The submission results are provided exactly as they are presented in the form so the launch times and sonde types need to be converted to the format used in the API.

This involves stripping the text from the sonde types to get the numerical id.

```javascript
var station_types = e.values[6].split(',');

if (e.values[6].length > 0) {
   for (var i = 0; i < station_types.length; i++) {
      matches = station_types[i].match(/\(([^)]+)\)/)
      if (matches != null && matches.length > 1) {
         station_types[i] = matches[1];
      }
   }
}
```

The process for launch times is more manual with only the most common times getting automatically converted to our UTC date format.

```
day:hour:minute
Daily 00Z -> 0:00:00
Monday 18Z -> 1:18:00
Sunday 12Z -> 7:12:00
```

```javascript
var station_times = e.values[7].split(',');

if (e.values[7].length > 0) {
   for (var i = 0; i < station_times.length; i++) {
      station_times[i] = station_times[i].trim();
      if (station_times[i] == "Daily 00Z") {
         station_times[i] = "0:00:00"
      }
      if (station_times[i] == "Daily 12Z") {
         station_times[i] = "0:12:00"
      }
   }
}
```

<h3>Check existing</h3>

The script attempts to download the existing records for the site if they exist to determine the differences between the current and proposed fields.

```javascript
var url = "https://api.v2.sondehub.org/sites?station=" + station_id;

var options = {
   "method": "GET",
   "contentType": "application/json"
};

var response = UrlFetchApp.fetch(url, options);
existing = JSON.parse(response.getContentText());
```

To compare the new entry it must be stored in the same format as served by the API.

This process will also import the launch times and sonde types if they exist for the station and the user hasen't proposed any changes to them.

The two objects can then be compared relatively easy using a generic difference function.

This <a href="https://stackoverflow.com/a/8596559/9389353" target="_blank">example</a> from Stack Overflow did the job well with some minor modifications.

The reason that such a function is required is due to arrays from the API being equal regardless of their actual order.

The function returns a new object which lists all the additions, modifications, and removals to individual properties from the existing entry

```json
{
   "27459": {
      "ascent_rate": {
         "type": "created",
         "data": 4.5
      },
      "burst_altitude": {
         "type": "created",
         "data": 27000
      },
      "descent_rate": {
         "type": "created",
         "data": 25
      }
   }
}
```

<h2>GitHub Issues</h2>

To post the suggested changes as a GitHub comment the data must be formatted as a single Markdown string.

<h3>Generate Markdown</h3>

The comment uses various GitHub Markdown features such as expanding modals and embeded maps.

The first section of the comment contains the raw unprocessed suggestions which allows for manual inspection and review.

The field names are bolded using `**` while new lines are created with `\n`.</p>

```javascript
"**Time Submitted:** " + e.values[0] + "\n"
```

<h3>Generate Map</h3>

The next section is an expandable map showing the proposed or existing solution of the launch site on an interactive map.

GitHub has <a href="https://github.blog/changelog/2022-03-17-mermaid-topojson-geojson-and-ascii-stl-diagrams-are-now-supported-in-markdown-and-as-files/" target="_blank">recently</a> added support for displaying geoJSON data on an interactive map in comments and files.

To show the launch site on the map a geoJSON file must be constructed using the provided coordinates and altitude.

````markdown
```geojson
{
   "type":"Feature",
   "geometry":{
      "type":"Point",
      "coordinates":[
         -58.5333,
         -34.8167,
         20
      ]
   },
   "properties":{
      "marker-size":"large",
      "marker-symbol":"observation-tower",
      "Station ID":"87576",
      "Station Name":"Ezeiza Aerodrome (Argentina)"
   }
}
```
````


The properties define the size and type of icon displayed on the map to indicates it's position along with some additional information such as station name and ID that is visible in a modal on icon selection.

The code used to generate this simply inserts the position along with station name and ID into the string.

<img src="{{"/assets/sites-map.png" | relative_url }}" style="display:block;margin:auto;max-width:100%;"/>

<h3>Hidden Modals</h3>

To reduce the overall size of the comment most of the data is hidden behind expandable modals which are created using the HTML details and summary tags.

```html
<details><summary>View map</summary>
   <p>Hidden Text!</p>
</details>
```

<h3>Raw JSON</h3>

The existing, suggested, and difference JSON files are also included in the comment under seperate modals for reference.

These are generated using `JSON.stringify()` and the same modals as above.

<h3>Publishing to GitHub</h3>

The GitHub API is used to create a new comment on the issue on the SondeHub Tracker with all the information.

The comments are published via my own personal account using an authorisation token.

<h2>Updating the Database</h2>

The Elasticsearch database containg the launch sites list is still manually updated to ensure no erros are introduced.

The automatic formatting of submissions into the correct JSON format simplifies this process considerably allowing for faster updates.