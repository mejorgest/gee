# gee

import geemap, ee
ee.Authenticate()
ee.Initialize()

Map=geemap.Map()
Map

#set centre
Map.setCenter(-85.10, 10.43);

#location geometry definition
# point=ee.Geometry.Point(-85.10, 10.43) # Commented out the old point definition

# Define the coordinates for the polygon
coords = [
    [-85.23711243785223, 10.378554657885585],
    [-85.2373055569013 , 10.370745117519215],
    [-85.22842208064398, 10.37030186749284 ],
    [-85.22805730021796, 10.378807937012752],
    [-85.23447314418158, 10.379230068435675],
    [-85.23711243785223, 10.378554657885585]
]

# Create an ee.Geometry.Polygon object
# Note: Removed the extra brackets around coords, Polygon expects a list of coordinates.
roi = ee.Geometry.Polygon(coords);

# Filter the image collection by the polygon (roi)
data=ee.ImageCollection("COPERNICUS/S2").filterBounds(roi);


image=ee.Image(data.filterDate("2023-01-01","2023-12-31").sort("CLOUD_COVERAGE_ASSESSMENT").first());


ndvi=image.expression(
"(NIR - RED) / (NIR + RED)",
{"NIR":image.select("B8"),
"RED":image.select("B4")}).rename('NDVI'); # Rename the band for clarity


# Clip the NDVI image to the region of interest
ndvi_clipped = ndvi.clip(roi)


display={
    "min":0,
    "max":1,
    "palette":[ 'red','orange', 'yellow','yellowgreen', 'green','black']
}

# Add the clipped NDVI layer to the map
Map.addLayer(ndvi_clipped, display, 'NDVI Clipped'); # Use the clipped layer
Map












