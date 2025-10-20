# GIS data workshop for REAL 6230, Fall 2025

Worshop 2025-10-20 by Keith Jenkins, GIS Librarian at Mann Library, Cornell University.

This document is online at: <https://kgjenkins.github.io/real-6230/>

All the data and documentation for this workshop can be downloaded from:\
<https://github.com/kgjenkins/real-6230/archive/main.zip>

After downloading the zip file, be sure to UNZIP it!  (Right-click > Extract All on Windows)


### GIS concepts

**GIS** stands for Geographic Information System (or Science)

"GIS data" refers to data with a spatial component -- it can be mapped!

**QGIS** (<https://qgis.org/>) is one of several popular GIS programs for mapping and spatial analysis.  Alternatives include ArcGIS, or programming languages like Python and R.

**Vector** data = points, lines, polygons.  Common file formats are GeoPackage (.gpkg) and Shapefile (.shp, .dbf, .prj, etc.)

**Raster** data = pixels (images, but also data).  Preferred file format is GeoTIFF (.tif), but .jpg and other formats can also be used.

**CRS** = Coordinate Reference System (or "projection")  Ideally, CRS details are quietly handled behind the scenes, so we barely even need to think about it.  But in the real world, you'll eventually run into CRS puzzles and problems, and CRS becomes even more important when doing distance- or area-based analysis.


### Getting Started

Hopefully you have already installed QGIS, which is freely available for Windows, Mac, and Linux at https://qgis.org/download/

- Open QGIS

We'll start with a boundary of our example location: Lexington, Kentucky.  The city of Lexington is coterminus with Fayette county, meaning that they share the same boundary.

- From the `data` folder, drag the `Fayette_County.gpkg` file onto the QGIS window

Colors and other style properties are not included in most data files, so QGIS assigns a random color.

To change the layer style:
- Open the Layer Styling panel – colorful paintbrush icon at top left of the Layers panel
- Change the color (click the back arrow at top to exit the color selector and return to the other styling options)
- Click "Simple fill" to change other properties, like stroke color and width


### Adding a basemap

We usually want to see our data within the context of a standard map.

Basemaps are web-based map images designed by professional cartographers who have already done the hard work of aggregating different data layers (streets, rivers, buildings, etc.) and customizing styles and labels to work at different zoom levels.

The QuickMapServices plugin may it easy to add different basemaps.  To use it, we first need to install the plugin.

- Plugins menu > Manage and Install Plugins...
- Scroll down, or search all plugins for "quickmap" (you don’t need to type the whole name)
- Select the QuickMapServices plugin
- Click the "Install plugin" button (it installs in seconds)
- Click "Close"

When you first install QuickMapServices, you'll want to get the full set of basemap definitions.

- Web menu > QuickMapServices > Settings
- "More services" tab > "Get contributed pack"

Let's add the OpenStreetMap basemap:

- Web menu > QuickMapServices > OSM > OSM Standard

The Fayette polygon is blocking the view of the basemap, so we'll change the style to let us focus our map on the county:

- In the layer styling panel, change from "Single Symbol" to "Inverted Polygons".
- Set the color to black and reduce the opacity to about 50%


### Adding library points

- From the `data` folder, drag the `libraries.gpkg` file onto the QGIS window
- Adjust the point style so that the points stand out from the basemap.  (For example, 3mm dark blue)

Get information about a point:

- Click "Identify Features" tool (blue circle with white "i"), select the layer, then click a polygon

View information about all the points as a table:

- Right-click layer in Layers pane > Open Attribute Table
- Click on a column header to sort by that column

The table and map are linked, so select a row from the table (by clicking the row number) to see it highlighted in yellow on the map. There is a toolbar button to "Deselect features from all layers".


### Adding demographic data

The US Census collects a wide variety of demographic data for the entire US.  Aggregate statistics are available at several geographic levels: nation, state, county, county subdivision, census tract, block group, and more.  This data can be downloaded from the official census.gov website, but it is often easier to get it from other sources such as Census Reporter (a free site built for journalists) or Simply Analytics (a subscription service that Cornell pays for).

Let's add some income data that has already been downloaded from Census Reporter as a shapefile, which is a format that dates from the early 1990s, which is why there are multiple component files (.shp, .dbf, .shx, .prj, and sometimes others). To add a shapefile to QGIS, we just need to select the .shp file and QGIS will take care of the rest.

- From the `data/census-report/median-household-income` folder, drag the `.shp` file onto the QGIS window
- Explore the attributes using the "Identify features" tool, or by opening the attribute table

The attribute names are a bit cryptic!  In this case, "B19013001" is the census variable ID for median household income.  The same ID with an "e" at the end is the estimated margin of error.  Note that downloads from Census Reporter include a metadata file that will help you know which variable was downloaded.

The SimplyAnalytics data is similar.  The example file contains two variables: total population, and median household income.  The income values are slightly different than the Census Reporter since they come from different years (and should not be directly compared, since SimplyAnalytics sometimes does their own estimates).


### Assign colors based on the income values

First, let's change the awful name of the income layer, and then change the style to show shades of green to represent the income values.

- Right-click the layer name > Rename Layer ... to "Median HH Income"
- In the Layer Styling panel, make sure the "Median HH Income" layer name is selected at the top
- Change "Single Symbol" to "Graduated"
- Set the "Value" to `B19013001` (the median HH income)
- Select the green color ramp (click the arrow at right)
- Set the "Mode" to "Equal Count (Quantile)"
- Click the "Classify" button, at which point QGIS will review the data values and calculate the value ranges for each class

Move the libraries point layer above the income layer so that it is visible.

- Which libraries appear to be in the lowest/highest income areas?


### Adding data from a spreadsheet

SimplyAnalytics has listings businesses that can be filtered by NAICS or SIC classifications and exported for specific regions.

Example data for full-service restaurants (NAICS code 722511) for Lexington has already been downloaded for you as a CSV file.  Dragging-and-dropping a CSV file onto QGIS will simply import the data as a table, so we will use a different method to add the data as a point layer based on the latitude and longitude values in the table.

- Click "Data Source Manager" button (also in Layer menu), select "Delimited Text" tab on left
- Click the "..." at the top right to browse to the `data/simply-analytics` folder and open the `naics722511-full-service-restaurants.csv` file
- Under "File Format", select "CSV"
- Under "Record and Fields Options", set the following:
    - Number of header lines to discard = 0
    - Check the box for "First record has field names"
    - Check the box for "Detect field types"
- Under "Geometry Definition", select "Point coordinates"
    - "X field" = 'Longitude'
    - "Y field" = 'Latitude'
    - "Geometry CRS" = 'EPSG:4326 - WGS 84' (this is GIS jargon for "standard latitude/longitude")
- Check the "Sample Data" preview of the table
- Click "Add", then close the data source manager

You can explore the restaurant points using the "Identify Features" tool, or open the attribute table.


### Coming soon ... on Wednesday!

- Analyzing point data by polygon
- Looking at parcel data to identify commercial properties
- Generating driving-distance (or walking-distance) isochrones
    - Sign up for a free OpenRouteService account at https://openrouteservice.org/ (click "Log In")
- Finding data sources


## Data sources

- County boundary: <https://data.lexingtonky.gov/datasets/def832a61a2b4565b3d12c1cdbd4c0fc_0/about>
- Libraries: <https://data.lexingtonky.gov/datasets/lfucg::library/about>
- Parcels: <https://hub.arcgis.com/datasets/e4a525d8772741468205e82fc173db22_0/about>
- Census Reporter: <https://censusreporter.org/>
- Simply Analytics (demographic data, business points): <https://catalog.library.cornell.edu/catalog/6168667>



### Finding data for other locations

- Consult the Wikipedia page for a city, to determine what county it is in.  General and GIS-specific data portals may be found at various administrative levels: city, county, state, and nation.
- Geolode, a catalog of open geodata websites: <https://geolode.org/>
- Guide to Finding GIS data: <https://cornell-gis.github.io/finding-gis-data/>
- For help via e-mail or a one-on-one consulation (Zoom or in-person), see <https://guides.library.cornell.edu/gis/help>
