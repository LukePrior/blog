---
title: "SondeHub Stats Badges"
subtitle: "Creating live status badges using Shields.io"
date: 2022-05-19 00:00:00 -0400
categories: [SondeHub]
---

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/swagger-ui/4.11.1/swagger-ui.css">
<script type="text/javascript" language="javascript" src="https://cdnjs.cloudflare.com/ajax/libs/swagger-ui/4.11.1/swagger-ui-bundle.js"></script>

<style>
   .badge_img {
      position: static!important;
      display: block!important;
      margin-right: auto!important;
      margin-left: auto!important;
      max-width: calc(66.6vw - 30px)!important;
      transform: scale(1.5)!important;
   }
</style>

This blog post contains instructions on how to use Shields.io with a custom API to create custom status badges.

<h2>Shields.io</h2>

Shields.io is a free tool that can be used to create metadata badges which can easily be added to open source projects.

The most common usecase is showing key project information such as usage and code coverage on sites like GitHub.

<h3>Creating Badges</h3>

Shields.io creates these metadata badges from the arguments provided in the URL and returns an image which can be embedded.

Several preconfigured badges exist for common services and statistics such as code analysis, quality, coverage, etc.

The following badge shows the last time this blog was updated.

```
https://img.shields.io/github/last-commit/LukePrior/blog
```

<img class="badge_img" src="https://img.shields.io/github/last-commit/LukePrior/blog">

<h3>Styling Badges</h3>

The badges all share the same styling options allowing for a consistent design across a site or project.

The five available styles for each badge include plastic, flat, flat-square, for-the-badge, and social.

```
https://img.shields.io/github/last-commit/LukePrior/blog?style=plastic
```

<img class="badge_img" src="https://img.shields.io/github/last-commit/LukePrior/blog?style=plastic&blank=blank">

```
https://img.shields.io/github/last-commit/LukePrior/blog?style=flat
```

<img class="badge_img" src="https://img.shields.io/github/last-commit/LukePrior/blog?style=flat&blank=blank">

```
https://img.shields.io/github/last-commit/LukePrior/blog?style=flat-square
```

<img class="badge_img" src="https://img.shields.io/github/last-commit/LukePrior/blog?style=flat-square&blank=blank">

```
https://img.shields.io/github/last-commit/LukePrior/blog?style=for-the-badge
```

<img class="badge_img" src="https://img.shields.io/github/last-commit/LukePrior/blog?style=for-the-badge&blank=blank">

```
https://img.shields.io/github/last-commit/LukePrior/blog?style=social
```

<img class="badge_img" src="https://img.shields.io/github/last-commit/LukePrior/blog?style=social&blank=blank">

<h3>Logo Badges</h3>

Icons can be included alongside text in badges by selecting an existing design from simple-icons or provided a custom base64-encoded image.

The simple-icons repository contains >2000 available icons which can be added to any badge by including their name in the logo field.

The complete list of available icons and their corresponding names can be found on the simple-icons repository <a href="https://github.com/simple-icons/simple-icons/blob/develop/slugs.md" target="_blank">here</a>.

```
https://img.shields.io/github/last-commit/LukePrior/blog?style=for-the-badge&logo=github
```

<img class="badge_img" src="https://img.shields.io/github/last-commit/LukePrior/blog?style=for-the-badge&logo=github&blank=blank">

To display a custom icon the image must be encoded in base64 with the total file size less then 8192 bytes so that it can fit within the request header.

```
https://img.shields.io/github/last-commit/LukePrior/blog?style=for-the-badge&logo=image/png;base64,...
```

<img class="badge_img" src="https://img.shields.io/github/last-commit/LukePrior/blog?style=for-the-badge&logo=image/png;base64,&blank=blank">

The horizontal padding around icons can be configured using the `logoWidth` field to allow for larger or smaller badges.

```
https://img.shields.io/github/last-commit/LukePrior/blog?style=for-the-badge&logo=github&logoWidth=40
```

<img class="badge_img" src="https://img.shields.io/github/last-commit/LukePrior/blog?style=for-the-badge&logo=github&logoWidth=40&blank=blank">

<h3>Colouring Badges</h3>

The individual sections of badges can be coloured including the logo and left/right sections of the badge.

These fields can all be styled using a variety of schemes including (hex, rgb, rgba, hsl, hsla, css named colours).

The logo colour can only be configured when using named icons from simple-icons by using the `logoColor` field.

```
https://img.shields.io/github/last-commit/LukePrior/blog?style=for-the-badge&logo=github&logoColor=red
```

<img class="badge_img" src="https://img.shields.io/github/last-commit/LukePrior/blog?style=for-the-badge&logo=github&logoColor=red&blank=blank">

The left side background colour can be set using the `labelColor` field.

```
https://img.shields.io/github/last-commit/LukePrior/blog?style=for-the-badge&labelColor=red
```

<img class="badge_img" src="https://img.shields.io/github/last-commit/LukePrior/blog?style=for-the-badge&labelColor=red&blank=blank">

The right side background colour can be set using the `color` field.

```
https://img.shields.io/github/last-commit/LukePrior/blog?style=for-the-badge&color=red
```

<img class="badge_img" src="https://img.shields.io/github/last-commit/LukePrior/blog?style=for-the-badge&color=red&blank=blank">

<h3>Custom badges</h3>

Shields.io can also generate a custom badge from any publically accessible JSON, XML, or YAML file.

The dynamic badge type accepts a data source and query along with options for text formatting.

```
https://img.shields.io/badge/dynamic/json?url=<URL>&label=<LABEL>&query=<$.DATA.SUBDATA>&color=<COLOR>&prefix=<PREFIX>&suffix=<SUFFIX>
```

The `url` field is used to specify the location of the data file that will be used to generate the badge.

The `query` field is used to select the correct subdata from the loaded file.

The `prefix` and `suffix` fields allow text to be added to the start and end of the loaded data.

This functionality was used to generate the badges in the SondeHub Wiki.

<h2>SondeHub Listener Stats API</h2>

The SondeHub Listener Stats API returns information about the number of receiver stations that have uploaded telemetry to the SondeHub radiosonde tracking database.

```
https://api.v2.sondehub.org/listeners/stats
```

```json
{
   "radiosonde_auto_rx": {
      "telemetry_count": 46867907,
      "unique_callsigns": 620,
      "versions": {
         "1.5.10": {
            "telemetry_count": 31942436,
            "unique_callsigns": 390
         }
      }
   },
   "rdzTTGOsonde": {
      "telemetry_count": 11768771,
      "unique_callsigns": 221
   },
   "totals": {
      "unique_callsigns": 847,
      "telemetry_count": 58737217
   }
}
```

The specific Python code and Elasticsearch Query for generating the API response can be found <a href="https://github.com/projecthorus/sondehub-infra/blob/a70f7aac4c3b4745a1894d9c7a261830ea982fa3/lambda/query/__init__.py#L394" target="_blank">here</a>.

The following Swagger UI component can be used to access the SondeHub Listener Stats API and get real results.

<div id="OpenAPI1"></div>

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
   const paths1 = {
      "/listeners/stats": {
         "get": {
            "tags": [
               "SondeHub Listener Stats"
            ],
            "summary": "Basic version stats",
            "description": "Use this to get stats on how many users are using specific software",
            "responses": {
               "200": {
                  "description": "Returns a dictionary of softwares and versions"
               },
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

This data is used to generate badges for the total number of stations along with counts for each software type.

The SondeHub Listener Stats API URL is provided along with a query to get the figure desired for each badge along with a suffix and prefix.

```
https://img.shields.io/badge/dynamic/json?label=Total&query=totals.unique_callsigns&suffix=%20Stations&url=https%3A%2F%2Fapi.v2.sondehub.org%2Flisteners%2Fstats&style=for-the-badge
```

<img class="badge_img" src="https://img.shields.io/badge/dynamic/json?label=Total&query=totals.unique_callsigns&suffix=%20Stations&url=https%3A%2F%2Fapi.v2.sondehub.org%2Flisteners%2Fstats&style=for-the-badge&blank=blank">

```
https://img.shields.io/badge/dynamic/json?label=radiosonde_auto_rx&query=radiosonde_auto_rx.unique_callsigns&suffix=%20Stations&url=https%3A%2F%2Fapi.v2.sondehub.org%2Flisteners%2Fstats&style=for-the-badge
```

<img class="badge_img" src="https://img.shields.io/badge/dynamic/json?label=radiosonde_auto_rx&query=radiosonde_auto_rx.unique_callsigns&suffix=%20Stations&url=https%3A%2F%2Fapi.v2.sondehub.org%2Flisteners%2Fstats&style=for-the-badge&blank=blank">

```
https://img.shields.io/badge/dynamic/json?label=rdzTTGOsonde&query=rdzTTGOsonde.unique_callsigns&suffix=%20Stations&url=https%3A%2F%2Fapi.v2.sondehub.org%2Flisteners%2Fstats&style=for-the-badge
```

<img class="badge_img" src="https://img.shields.io/badge/dynamic/json?label=rdzTTGOsonde&query=rdzTTGOsonde.unique_callsigns&suffix=%20Stations&url=https%3A%2F%2Fapi.v2.sondehub.org%2Flisteners%2Fstats&style=for-the-badge&blank=blank">

```
https://img.shields.io/badge/dynamic/json?label=SondeMonitor&query=SondeMonitor.unique_callsigns&suffix=%20Stations&url=https%3A%2F%2Fapi.v2.sondehub.org%2Flisteners%2Fstats&style=for-the-badge
```

<img class="badge_img" src="https://img.shields.io/badge/dynamic/json?label=SondeMonitor&query=SondeMonitor.unique_callsigns&suffix=%20Stations&url=https%3A%2F%2Fapi.v2.sondehub.org%2Flisteners%2Fstats&style=for-the-badge&blank=blank">

<h2>SondeHub Recovery Stats API</h2>

The SondeHub Recovery Stats API returns information about the number of radiosondes that have been marked as retrieved in the SondeHub database.

```
https://api.v2.sondehub.org/recovered/stats
```

```json
   {
      "total": 5223,
      "recovered": 4823,
      "failed": 486,
      "chaser_count": 1668,
      "top_chasers": {
         "LZ4TU": 93,
         "TFDHU": 83,
         "Lobelt": 54,
         "EDDB": 53
      }
   }
```

The specific Python code and Elasticsearch Query for generating the API response can be found <a href="https://github.com/projecthorus/sondehub-infra/blob/a70f7aac4c3b4745a1894d9c7a261830ea982fa3/lambda/recovered/__init__.py#L255" target="_blank">here</a>.

The following Swagger UI component can be used to access the SondeHub Listener Stats API and get real results.

<div id="OpenAPI2"></div>

<script>
   const paths2 = {
      "/recovered/stats": {
         "get": {
            "tags": [
               "SondeHub Recovery Stats"
            ],
            "summary": "Request Recovery Stats",
            "description": "Use this to get the recovery stats",
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
                  "description": "Longitude - if specified, lat and distance are required Eg:  138.6007",
                  "type": "number"
               },
               {
                  "in": "query",
                  "name": "distance",
                  "description": "Distance in meters - if specified, lat and lon are required",
                  "type": "number"
               },
               {
                  "in": "query",
                  "name": "duration",
                  "description": "How far back to search in seconds. Defaults to forever",
                  "type": "number"
               },
               {
                  "in": "query",
                  "name": "datetime",
                  "description": "End time to query as an ISO-8601 time string. Defaults to now. Example: `2021-02-02T11:27:38.634Z`",
                  "type": "string",
                  "format": "date-time"
               }
            ],
            "responses": {
               "200": {
                  "description": "Returns a list of recovery objects"
               },
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

This data is used to generate badges for the total number of sondes reported and the count of unique finders.

The SondeHub Recovery Stats API URL is provided along with a query to get the figure desired for each badge along with a suffix and prefix.

```
https://img.shields.io/badge/dynamic/json?label=Reported&query=total&suffix=%20Sondes&url=https%3A%2F%2Fapi.v2.sondehub.org%2Frecovered%2Fstats&style=for-the-badge
```

<img class="badge_img" src="https://img.shields.io/badge/dynamic/json?label=Reported&query=total&suffix=%20Sondes&url=https%3A%2F%2Fapi.v2.sondehub.org%2Frecovered%2Fstats&style=for-the-badge&blank=blank">

```
https://img.shields.io/badge/dynamic/json?label=Recovered&query=recovered&suffix=%20Sondes&url=https%3A%2F%2Fapi.v2.sondehub.org%2Frecovered%2Fstats&style=for-the-badge
```

<img class="badge_img" src="https://img.shields.io/badge/dynamic/json?label=Recovered&query=recovered&suffix=%20Sondes&url=https%3A%2F%2Fapi.v2.sondehub.org%2Frecovered%2Fstats&style=for-the-badge&blank=blank">

```
https://img.shields.io/badge/dynamic/json?label=Lost&query=failed&suffix=%20Sondes&url=https%3A%2F%2Fapi.v2.sondehub.org%2Frecovered%2Fstats&style=for-the-badge
```

<img class="badge_img" src="https://img.shields.io/badge/dynamic/json?label=Lost&query=failed&suffix=%20Sondes&url=https%3A%2F%2Fapi.v2.sondehub.org%2Frecovered%2Fstats&style=for-the-badge&blank=blank">

```
https://img.shields.io/badge/dynamic/json?label=Chasers&query=chaser_count&suffix=%20Chasers&url=https%3A%2F%2Fapi.v2.sondehub.org%2Frecovered%2Fstats&style=for-the-badge
```

<img class="badge_img" src="https://img.shields.io/badge/dynamic/json?label=Chasers&query=chaser_count&suffix=%20Chasers&url=https%3A%2F%2Fapi.v2.sondehub.org%2Frecovered%2Fstats&style=for-the-badge&blank=blank">

<h2>Usage with GitHub Markdown</h2>

These badges can easily be added to any GitHub README, Wiki page, or comment using markdwon.

```markdown
![This is an example badge][https://img.shields.io/github/last-commit/LukePrior/blog]
```

<p>The badges can also be set as hyperlinks with the following markdown.</p>

```markdown
[![This is an example badge][https://img.shields.io/github/last-commit/LukePrior/blog]](https://github.com/LukePrior/blog)
```