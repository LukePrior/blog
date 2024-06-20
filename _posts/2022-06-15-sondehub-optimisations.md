---
title: "SondeHub Tracker Optimisations"
subtitle: "Improving speeds with modern web technologies"
date: 2022-06-15 00:00:00 -0400
img_path: /assets/img/
categories: [SondeHub]
---

The SondeHub Tracker is an updated fork of the Habhub Tracker to utilise the new SondeHub OpenSearch Radiosonde database.

The original Habhub Tracker was created over 10 years ago and contains numerous legacy features that impact performance on modern systems.

The original tracker was not intended to handle the high volume of live flights now recorded in the SondeHub database from over 800 stations.

This increased load warranted several major modifications to the code to switch to newer and faster technologies.

## Leaflet

The Habhub Tracker uses the Google Maps API which since June 2018 had begun costing a considerable amount to use after Google updated the pricing structure.

This cost consideration along with a desire to switch to a faster open-source alternative drove the decision to switch to Leaflet for the tracker.

This migration required considerable effort as several Google Maps API features didn't have compatible replacements in Leaflet so they had to be redesigned or created.

The major changes included duplicating the custom controls for time-period selection, map switching, and map scale.

This switch brought a considerable performance improvement to the tracker before any specific optimisations were implemented.

## Canvas Rendering

The next major factor limiting the performance of the tracker was rendering the increased number of station icons and balloon paths as the network had grown.

These issues could be addressed by switching to canvas rendering by changing the Leaflet default which resulted in an instant improvement.

The existing icons couldn't be easily rendered on the canvas and had also begun to clutter the interface so the decision to switch to a minimalistic circular design that could be drawn to the canvas.

The new design resulted in a significant reduction in the number of items that had to be individually rendered during map operations allowing smooth panning and zooming of the map.

This new icon style also allowed the tracker to have a more cohesive design with launch site markers featuring a similar design.

The actual balloon icons couldn't be easily rendered on the canvas so these remain an area for further improvement.

## WebSockets

The first iteration of the SondeHub tracker aimed to closely replicate the Habhub tracker so all existing API endpoints were duplicated and modified to query the new SondeHub database.

These existing API endpoints were not designed with the frequency and quantity of data now received by SondeHub in mind so they required updating to reduce operating costs and improve speeds.

The decision was made to switch to a Websockets-based solution so that live telemetry data could be streamed to the tracker allowing for faster updates and higher data resolution.

This switch also came with the benefit of significantly reduced operating costs which allowed the tracker to remain sustainable as active users increased.

The tracker code required several modifications so that the existing API endpoints would only be called on page load to fetch historical data while WebSockets would be used for all future data.

The WebSockets stream only includes flight data with other information such as flight predictions still retrieved by regularly polling specific API endpoints.

## Time Periods

The Habhub tracker included selectable time periods for flight data of up to 3 days which would result in the SondeHub tracker crashing as this would contain over 1000 flights.

The default time period was reduced to 3 hours on the SondeHub tracker which is suitable for viewing the most recent flights without significantly impacting performance.

The introduction of WebSockets allowed for the addition of a new live time period that only displays data received from WebSockets without fetching any historical info.

This mode is extremely fast and still allows for the entire history of individual flights to be loaded if manually selected.

## Visibility Configuration

The option to disable various map layers was added so that only the desired information would be displayed which results in performance improvements when not all layers are enabled.

The receiver stations, launch sites, chase cars, horizon rings, and titles can all be individually hidden with the user preference remembered between sessions.

These changes can also be applied without requiring a full refresh of the tracker ensuring that specific entities can quickly be checked.

## Selective Display

These performance improvements made the tracker responsive in modern desktop environments however on certain mobile devices the tracker could still suffer during peak times.

The suggestion to only update and display information for flights currently visible on the screen allowed for time savings in the rendering and calculation pipeline increasing responsiveness.

The map bounds are recorded and new flight positions are checked against these bounds and if excluded the calculation of certain visual elements such as graphs and sidebar inclusion are skipped.

This feature can also improve the user experience by improving navigation and finding the desired information for a local area when there are hundreds of live flights globally.

## Further Optimisations

The SondeHub tracker is still in active development and additional team members are working on further optimisations to improve the user experience which can be read about [here](https://www.patreon.com/posts/frustum-culling-87370094).