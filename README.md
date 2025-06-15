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


image = ee.Image(data.filterDate("2025-01-01", "2025-12-31").sort("CLOUD_COVERAGE_ASSESSMENT").sort('system:time_start', False).first());

# Obtener la fecha de la imagen
image_date_millis = image.get('system:time_start')
image_date = ee.Date(image_date_millis).format('YYYY-MM-dd') # Formato Año-Mes-Día

# Obtener la información del servidor e imprimirla
print('Fecha de la imagen seleccionada:', image_date.getInfo())

# También puedes ver la nubosidad para confirmar que elegiste la menos nubosa
cloud_cover = image.get('CLOUD_COVERAGE_ASSESSMENT')
print('Evaluación de cobertura nubosa:', cloud_cover.getInfo())

# Calcular el área en metros cuadrados (ejecución en el servidor GEE)
area_m2_server = roi.area()

# Obtener el valor del área del servidor
area_m2_client = area_m2_server.getInfo()

# Convertir a hectáreas (1 ha = 10,000 m²)
area_ha = area_m2_client / 10000

# Convertir a kilómetros cuadrados (1 km² = 1,000,000 m²)
area_km2 = area_m2_client / 1000000

# Imprimir los resultados
print(f"Área de la ROI seleccionada:")
print(f"- Metros cuadrados: {area_m2_client:,.2f} m²")
print(f"- Hectáreas: {area_ha:,.2f} ha")
print(f"- Kilómetros cuadrados: {area_km2:,.2f} km²")
