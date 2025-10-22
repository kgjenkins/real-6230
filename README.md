# GIS data workshop for REAL 6230, Fall 2025

Workshop 2025-10-20 and 2025-10-22 by Keith Jenkins, GIS Librarian at Mann Library, Cornell University.

This document is online at: <https://kgjenkins.github.io/real-6230/>

All the data and documentation for this workshop can be downloaded from:\
<https://github.com/kgjenkins/real-6230/archive/main.zip>

Note that this file has been UPDATED for Day 2.  After downloading the zip file, be sure to UNZIP it!  (Right-click > "Extract All" on Windows)


### GIS concepts

**GIS** stands for Geographic Information System (or Science)

"GIS data" refers to data with a spatial component -- it can be mapped!

**QGIS** (<https://qgis.org/>) is one of several popular GIS programs for mapping and spatial analysis.  Alternatives include ArcGIS, or programming languages like Python and R.

**Vector** data = points, lines, polygons.  Common file formats are GeoPackage (.gpkg) and Shapefile (.shp, .dbf, .prj, etc.)

**Raster** data = pixels (images, but also data).  Preferred file format is GeoTIFF (.tif), but .jpg and other formats can also be used.

**CRS** = Coordinate Reference System (or "projection")  Ideally, CRS details are quietly handled behind the scenes, so we barely even need to think about it.  But in the real world, you'll eventually run into CRS puzzles and problems, and CRS becomes even more important when doing distance- or area-based analysis.


# Day 1

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


# Day 2

### Parcel Data

Many cities and counties in the US provide open access to their parcel data.  (Others may sell or not provide such data at all.)  Depending on the provider, parcel data may or may not include ownership or assessment values.  The data for Lexington does not, but it does include an attribute field called "CLASS" which contains a one-letter code.  Unfortunately, they don't seem to provide any metadata explaining these values, but inspecting the data, we can guess that C=commercial, R=residential, F=farming?, E=exempt?, M=mixed?

- From the `data` folder, drag the `parcels.gpkg` file onto the QGIS window
- Open the Layer Styling panel – colorful paintbrush icon at top left of the Layers panel
- Change "Single Symbol" to "Categorized"
    - Set Value = "CLASS"
    - Click the "Classify" button, at which point QGIS will find all the unique values in the "CLASS" column
- Toggle the check boxes to turn individual values on/off (for example, to only display the "C" values on the map)


### Analysis: count restaurants within each blockgroup

There are often many different ways to analyze datasets.  For example, suppose we want to understand how many restaurants are available to people living in different census blockgroups.  The simplest method might be to count the number of restaurant points that are contained within each blockgroup polygon:

- Open the Processing toolbox (Processing menu > Toolbox)
- Search for "count points in polygon" and open that tool
- In the tool dialog, set the following:
    - Polygons = "median hh income by blockgroup"
    - Points = "restaurants"
    - Count field name = 'restaurants' (this sets the name of the new attribute field that will be added)
- Click the "Run" button

This will generate a new, temporary layer called "Count" that is a copy of the original blockgroup polygons, but with the new field added.

- Move the new "Count" layer below the restaurant points
- Adjust the polygon color as desired (some color that works well with the blue points)
- Use the "Identify features" tool to spot-check a few polygons to see if the "restaurants" count seems correct.
  (Note that sometimes there can be multiple points at the same exact location such that it may look like a single point.)

What are the weaknesses of this metric we have created?


### Analysis: generate a heatmap of restaurant density

A heatmap is a visualization tool that helps to identify hotspots in large point datasets where there might be many points on top of each other, in which case a map of points may be misleading.  The heatmap tool in QGIS allows us to count how many points are within a specific distance of a given location.  In order to specify the distance, we will need to reproject the point layer into a feet- or meters-based coordinate system:

- Open the Processing toolbox (Processing menu > Toolbox)
- Search for "reproject" and open the "Reproject layer" tool (under "Vector general")
- In the tool dialog, set the following:
    - Input layer = "restaurants"
    - Target CRS = "Project CRS: EPSG: 2246 - NAD83 / Kentucky North (ftUS)"
- Click the "Run" button

This will create a new, temporary layer called "Reprojected".  It should look just like the original "restaurant" point layer (except for the point style, which will likely be different).  We will use this new layer to generate the heatmap:

- Open the Processing toolbox (Processing menu > Toolbox)
- Search for "heatmap" and open the "Heatmap (Kernel Density Estimation)"
- In the tool dialog, set the following:
    - Point layer = "Reprojected"
    - Radius = 1 miles (this is the distance within which it will look for points)
    - Pixel size X = 500, Pixel size Y = 500 (this is in the units of the source CRS, so feet in this case)
    - Under Advanced Parameters, set:
        - Kernel shape = Uniform (this will give us exact counts)
- Instead of saving to a temporary file, let's save it directly to a file:
    - Click the "..." and browse to your data folder, and save it as "restaurants-1mile.tif"
- Click the "Run" button

This will create a new raster layer called "restaurants-1mile".  Let's style it:

- Open the Layer Styling panel and select the "restaurants-1mile" layer
- Change "Singleband gray" to "Singleband pseudocolor"
- Set the Color ramp to "Magma"


### Analysis: zonal statistics

Whenever we have a numeric data raster, we can calculate zonal statistics for a polygon layer, which will summarize all the pixel values within each polygon.

- In the processing toolbox, search for "zonal" and open the "Zonal statistics" tool
- In the tool dialog, set the following:
    - Input layer = "Count" (which is our blockgroups with the count of points within each polygon)
    - Raster layer = "restaurants-1mile"
    - Output column prefix = 'rest1mile_'
    - Statistics to calculate: select Mean, Median, Minimum, Maximum
- Click the "Run" button

This will create new temporary layer called "Zonal Statistics".  Use the "Identify Features" tool and click a polygon.  The "count" field shows how many of the raster pixels were used in the calculation for this polygon.  The other values were calculated from the set of values in those pixels across the extent of the polygon, and provide a summary of walking access to restaurants within the blockgroup.

If the results seem correct, we can save this temporary layer to a file, 

- Right-click the "Zonal Statistics" layer name > Make Permanent...
- Set Format = GeoPackage
- For the File name, click the '...' and browse to your data folder and save it as "blockgroup-zonalstats.gpkg"

We can also export the attribute table of this layer as a CSV file so that it can be further analyzed using statistical software such as Stata:

- Right-click the "Zonal Statistics" layer name > Export > Save Features As...
- Set Format = Comma Separated Value (CSV)
- For the File name, click the '...' and browse to your data folder and save it as "blockgroup-zonalstats.csv"


### Isochrones

An isochrone is a type of map that shows areas accessible from (or to) a point within a certain amount of time.  For example, we could show which areas of Lexington are within a 30 minute walk to a library.  Although it is possible to generate isochrones using your own road network data, you would also need to estimate travel time for different types of roads and intersections, which gets complicated very quickly.  So instead, we will use a free web service from OpenRouteService to calculate the isochrones, which will isolate us from that complexity, but we will have to work within their API limits (500 isochrone requests per day, 20 per minute).

- Go to [openrouteservice.org](https://openrouteservice.org/)
- Click the "Log in" link at top right
- Create an account and log in
- After logging in, copy your API Key so we can use it in QGIS

Next, we need to add a plugin to QGIS:

- Plugins menu > Manage and Install Plugins...
- Search plugins for "ORS Tools" (if you don't find it, be sure "All" is selected on the left)
- Select and install ORS Tools
- Web menu > ORS Tools > Provider Settings...
- Paste the API Key you copied from the website
- Click "Save"

The ORS Tools plugin adds a button to the toolbar, and also adds several tools to the Processing Toolbox.  Let's create some isochrones!

- In the Processing Toolbox, search for and open the "Isochrones from Point-Layer" tool
- In the tool dialog, set the following:
    - Travel mode = "foot-walking"
    - Input Point layer = "Library"
    - Input layer ID Field = OBJECTID
    - Dimension = time
    - Comma-separated ranges = 30
- Click the "Run" button

The tool will send each point off to the ORS API and turn the results into a new polygon layer.  The library layer only has 6 points, so there should be no issue with the limit of 20 requests per minute.

Each polygon shows the approximate area within a 30 minute walk of the library near the center of the polygon.  Use the "Identify Features" tool to explore the attributes (or open the attribute table) and notice that one nice additional piece of information included is "TOTAL_POP", an estimate of the number of people within the isochrone.

If you ever want to calculate an isochrone from an arbitrary point (such as a potential new location for a business), you can use the "Isochrones from Point" tool and click the point on the map.


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
