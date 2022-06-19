---
title: "SondeHub Radiosondy Ingest"
subtitle: "Importing Recovery Data from Radiosondy.info"
date: 2022-06-18 00:00:00 -0400
img_path: /assets/img/
categories: [SondeHub]
---

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/swagger-ui/4.11.1/swagger-ui.css">
<script type="text/javascript" language="javascript" src="https://cdnjs.cloudflare.com/ajax/libs/swagger-ui/4.11.1/swagger-ui-bundle.js"></script>

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
   .swagger-ui input {
      background-color: var(--btn-box-shadow)!important;
   }
</style>

The <a href="https://sondehub.org/" target="_blank">SondeHub Tracker</a> and <a href="https://radiosondy.info/" target="_blank">Radiosondy.info</a> site are the two major tracking tools for global radiosonde flights with both featuring hundreds of stations and users.

The sites both include functionality for users to report recovery information to a central database which can be used to generate leaderboards and reduce waste.

<h2>Reporting Recoveries</h2>

The primary concept around reporting recoveries is assigning the recovery to a specific radiosonde and user.

This requires that the recovered Radiosonde has been previously tracked so that the serial information is available.

The recovery can then be lodged against the specific serial along with information regarding the finder and the process.

This information can be used to keep track of which radiosondes may still be available to recover and allow users to compare recoveries.

<h2>Radiosonde Serials</h2>

The sites both support a variety of radiosondes created by various manufacturers and launched globally.

The telemetry formats for these devices are often not publically documented so decoders are often created from limited data.

This lack of a formal specification for radiosondes can result in the decoded data from different programs for the same flight not being equal.

This inconsistency is addressed by limiting which devices can upload to each network but introduces complexitity when attempting to transfer data between sites.

The most popular radiosonde is the Viasala RS41 accounting for approximately 85% of global launches with most decoders producing the same output for all non-temperature related fields.

The other supported radiosondes often include less similar decoding algorithms between programs including different ways of generating a unique serial number.

To succesfully import radiosonde recovery data the serial numbers have to be matched between the two sites.

<h2>Getting Recoveries</h2>

The private Radiosondy.info recovery API is used to programatically get the recovery reports.

The API requires an API token and a time period in hours to return recovery reports for.

```
https://radiosondy.info/api/v1/sonde-logs?token={token}&period=48
```

```json
{
   "results": [
      {
         "radiosonde": {
               "number": "ME1622EBB",
               "type": "M10",
               "aux": "NO",
               "qrg": "403.400",
               "start_place": "Murcia (ES)"
         },
         "log_info": {
               "status": "FOUND",
               "finder": "azofaifo",
               "added_by": "azofaifo",
               "log_added": "2022-06-18 10:18:02",
               "comment": "In the corner of a house, in a small town 2 minutes from where I live.",
               "found_coordinates": {
                  "latitude": "38.0989",
                  "longitude": "-1.2775"
               }
         },
         "start_time": "2022-06-17 23:18:52",
         "nearest_city": "Ceuti (ES)"
      }
   ]
}
```

<h3>Swagger UI API</h3>

The following Swagger UI component can be used to access the Radiosondy.info Recovery API and get real results.

<div id="OpenAPI0"></div>

<script>
   const paths0 = {
      "/sonde-logs": {
         "get": {
            "tags": [
               "Radiosondy.info Recovered"
            ],
            "summary": "Radiosondy.info Recovered",
            "description": "Request recovery reports from Radiosondy.info..",
            "parameters": [
               {
                  "in": "query",
                  "name": "token",
                  "type": "string",
                  "description": "API token used to grant access to API"
               },
               {
                  "in": "query",
                  "name": "period",
                  "type": "number",
                  "description": "Time in hours to retrieve recovery reports"
               }
            ],
            "responses": {
               "200": {
                  "description": "Returns a dictionary with a list of recovery reports"
               }
            },
         }
      }
   };

   const spec0 = {
      'swagger': '2.0',
      'paths': paths0,
      'host': 'radiosondy.info',
      'basePath': '/api/v1'
   };

   SwaggerUIBundle({
      spec: spec0,
      domNode: document.querySelector('#OpenAPI0')
   })
</script>

<h2>Mapping Serials</h2>

The recovery data from Radiosondy.info includes information about the specific radiosonde and the finder.

The radiosonde data includes the specific model which is used to check if the serial can be matched directly.

When the serial needs to be matched the provided radiosonde information is used with the SondeHub Sondes API to search for a match.

The recovery position and launch time are used to search for any data packets in the SondeHub database within 2000m of the provided location in a three hour window.

```python
searchParams = "?lat={}&lon={}&distance=2000&last={}".format(lat, lon, searchSeconds)
```

The results of this search are then checked to see if the radiosonde type and transmission frequency match what was reported by Radiosondy.info

```python
if value["type"] in sondeType:
    if abs(float(sondeFrequency) - float(value["frequency"])) < 0.05:
```

These parameters allow the radiosonde serial to be matched with a high degree of certainty.

```
https://api.v2.sondehub.org/sondes?lat=-34.94449&lon=138.91148&distance=2000
```

```json
{
   "106-2-13771":{
      "2022-06-18T01:22:08.000Z":{
         "software_name":"radiosonde_auto_rx",
         "software_version":"1.5.10",
         "uploader_callsign":"EA5BZ",
         "uploader_position":"38.2768,-0.6711",
         "uploader_antenna":"1/4 wave monopole",
         "time_received":"2022-06-18T01:22:08.746528Z",
         "datetime":"2022-06-18T01:22:08.000000Z",
         "manufacturer":"Meteomodem",
         "type":"M10",
         "serial":"106-2-13771",
         "frame":1339550546,
         "lat":38.09909,
         "lon":-1.27771,
         "alt":526.16,
         "temp":27.4,
         "humidity":27.4,
         "vel_v":-10.31,
         "vel_h":1.04389,
         "heading":113.12652,
         "sats":11,
         "batt":4.97,
         "frequency":403.404,
         "snr":4.8,
         "user-agent":"Amazon CloudFront",
         "position":"38.09909,-1.27771",
         "upload_time_delta":-1.168,
         "uploader_alt":120.0,
         "uploaders":[
            {
               "uploader_callsign":"EA5BZ",
               "frequency":403.404,
               "snr":4.8
            }
         ]
      }
   }
}
```

<h3>Swagger UI API</h3>

The following Swagger UI component can be used to access the SondeHub Sondes API and get real results.

<div id="OpenAPI1"></div>

<script>
   const paths1 = {
      "/sondes": {
         "get": {
            "tags": [
               "SondeHub Sondes"
            ],
            "summary": "SondeHub Sondes",
            "description": "Request latest sonde data indexed by serial number, with options for position/distance based-filtering.",
            "parameters": [
               {
                  "in": "query",
                  "name": "lat",
                  "type": "number",
                  "description": "Latitude - if specified, lon and distance are required. Eg: -34.9285"
               },
               {
                  "in": "query",
                  "name": "lon",
                  "type": "number",
                  "description": "Longitude - if specified, lat and distance are required Eg: 138.6007"
               },
               {
                  "in": "query",
                  "name": "distance",
                  "type": "number",
                  "description": "Distance in meters - if specified, lat and lon are required"
               },
               {
                  "in": "query",
                  "name": "last",
                  "type": "number",
                  "description": "How far back to search in seconds. Defaults to 24hrs"
               }
            ],
            "responses": {
               "200": {
                  "description": "Returns a dictionary keyed by serial number of a dictionary of times with SondeHub Telemetry values"
               }
            },
         }
      }
   };

   const spec1 = {
      'swagger': '2.0',
      'paths': paths1,
      'host': 'api.v2.sondehub.org'
   };

   SwaggerUIBundle({
      spec: spec1,
      domNode: document.querySelector('#OpenAPI1')
   })
</script>

<h2>Existing Reports</h2>

Some users may report their radiosonde recovery to both sites in which case the Radiosondy.info recovery should not be imported unless it contains new information.

The recovery report can either be classified as found or lost depending on if the radiosonde was succesfully retrieved.

A single radiosonde can have multiple recovery reports if the first attempt did not result in retrieval but a subsequent attempt did.

The decoded or matched serial is used with the SondeHub Recovery API to check for existing reports.

```python
recoveryCheckParams = "?serial={}".format(serial)
```

The status of the latest report is checked and if a valid recovery already exists the import process for that specific radiosonde is skipped.

```
https://api.v2.sondehub.org/recovered?serial=106-2-13771
```

```json
[
   {
      "datetime":"2022-06-18T10:18:02",
      "serial":"106-2-13771",
      "lat":38.0989,
      "lon":-1.2775,
      "recovered":true,
      "recovered_by":"azofaifo",
      "description":"In the corner of a house, in a small town 2 minutes from where I live. [via Radiosondy.info]",
      "position":[
         -1.2775,
         38.0989
      ]
   }
]
```

<h3>Swagger UI API</h3>

The following Swagger UI component can be used to access the SondeHub Recovery API and get real results.

<div id="OpenAPI2"></div>

<script>
   const paths2 = {
      "/recovered": {
         "get": {
            "tags": [
               "SondeHub Recovered"
            ],
            "summary": "SondeHub Recovered",
            "description": "Use this to get the recovery data.",
            "parameters": [
                {
                  "in": "query",
                  "name": "serial",
                  "type": "string",
                  "description": "radiosonde serial number (or multiple serial numbers separated by a comma) to filter on. If none provided all serials will be presented."
               },
               {
                  "in": "query",
                  "name": "lat",
                  "type": "number",
                  "description": "Latitude - if specified, lon and distance are required. Eg: -34.9285"
               },
               {
                  "in": "query",
                  "name": "lon",
                  "type": "number",
                  "description": "Longitude - if specified, lat and distance are required Eg: 138.6007"
               },
               {
                  "in": "query",
                  "name": "distance",
                  "type": "number",
                  "description": "Distance in meters - if specified, lat and lon are required"
               },
               {
                  "in": "query",
                  "name": "last",
                  "type": "number",
                  "description": "How far back to search in seconds. Defaults to 24hrs"
               }
            ],
            "responses": {
               "200": {
                  "description": "Returns a list of recovery objects"
               }
            },
         }
      }
   };

   const spec2 = {
      'swagger': '2.0',
      'paths': paths2,
      'host': 'api.v2.sondehub.org'
   };

   SwaggerUIBundle({
      spec: spec2,
      domNode: document.querySelector('#OpenAPI2')
   })
</script>

<h2>Creating Report</h2>

The recovery report can be created once a valid serial has been found and the SondeHub database has been checked for existing valid reports.

This requires that the provided fields from Radiosondy.info are parsed and translated into the required fields for SondeHub.

The finder, time, description, position, and serial are all extracted and submitted as a new recovery to SondeHub using the API.

<h2>Automation</h2>

The complete Python script used to complete this process can be found <a href="https://github.com/projecthorus/sondehub-infra/blob/main/lambda/recovery_ingest/__init__.py#L9" target="_blank">here</a>.

The script is hosted on AWS alongisde other SondeHub infrastructure and is run on the hour with aproximately 30 recoveries imported each day.

The ingestion program has seen >5000 reports imported over the previous months with Radiosondy.info also implementing a asystem to import SondeHub reports.

This has resulted in almost universal coverage of recovery reports across the two sites improving the experience for all users.