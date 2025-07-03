1. เพิ่ม CDN JS/CSS ของ Maplibre ไปยังส่วนของ header ของ Html
```
<!-- Maplibre core CSS/JS -->
<script src="https://unpkg.com/maplibre-gl@^5.6.1/dist/maplibre-gl.js"></script>
<link href="https://unpkg.com/maplibre-gl@^5.6.1/dist/maplibre-gl.css" rel="stylesheet" />
```

2. เพิ่ม style map และ div content id map จากนั้นเรียก script ใช้งาน Maplibre

```
html,
body {
    height: 100%;
    margin: 0;
}

#map {
    height: 600px;
    width: 1000px;
}
```


3. เพิ่ม style map และ div content id map จากนั้นเรียก script ใช้งาน Maplibre

```
<div id="map"></div>

const map = new maplibregl.Map({
    container: 'map',
    style: "https://demotiles.maplibre.org/style.json",
    center: [100.5784086, 13.8455641],
    zoom: 5
});
```

4. สร้าง Folder basemap จากนั้น สร้างไฟล์ ghyb.json
```
{
    "version": 8,
    "sources": {
        "google-hybrid": {
            "type": "raster",
            "tiles": [
                "https://mt1.google.com/vt/lyrs=s&x={x}&y={y}&z={z}"
            ],
            "tileSize": 256
        }
    },
    "layers": [
        {
            "id": "google-layer",
            "type": "raster",
            "source": "google-hybrid"
        }
    ]
}
```

5. เรียกใช้ไฟล์ ghyb.json ใน script
```
const googleHybrid: "./basemap/ghyb.json"
const map = new maplibregl.Map({
    container: 'map',
    style: googleHybrid,
    center: [100.5784086, 13.8455641],
    zoom: 5
});
```

6. เพิ่ม ตำแหน่ง marker และ ข้อมูล Popup จากนั้นเรียกใช้บนแผนที่
```
const markerData = [
            {
                coords: [100.5784086, 13.8455641],
                popup: '<b>สำนักงานการวิจัยแห่งชาติ (วช.)</b><br/>196 ถ. พหลโยธิน แขวงลาดยาว เขตจตุจักร กรุงเทพมหานคร 10900'
            },
            {
                coords: [100.574267, 13.84567],
                popup: '<b>คณะวนศาสตร์ มหาวิทยาลัยเกษตรศาสตร์</b><br/>50 ถ. พหลโยธิน แขวงลาดยาว เขตจตุจักร กรุงเทพมหานคร 10900'
            },
            {
                coords: [100.5820885, 13.849543],
                popup: '<b>กรมวิทยาศาสตร์ทหารบก</b><br/>2398/80 ถ. พหลโยธิน แขวงเสนานิคม เขตจตุจักร กรุงเทพมหานคร 10900'
            },
            {
                coords: [100.40828979212644, 13.6709446670883],
                popup: '<b>แม็คโคร บางบอน</b><br/>210 ถ. กาญจนาภิเษก แขวงคลองบางพราน เขตบางบอน กรุงเทพมหานคร 10150'
            },
            {
                coords: [100.40699726959333, 13.66350398204581],
                popup: '<b>โรงพยาบาลบางปะกอก 8</b><br/>1055 ถ. เอกชัย แขวงคลองบางบอน เขตบางบอน กรุงเทพมหานคร 10150'
            }
];

markerData.forEach(({ coords, popup }) => {
    const marker = new maplibregl.Marker()
        .setLngLat(coords)
        .setPopup(
            new maplibregl.Popup({ offset: 25 }) // Optional: offset for better positioning
        .setHTML(popup)
        )
        .addTo(map)
});
```

7. เรียกใช้งานข้อมูล Geojson จากไฟล์
```
fetch('./district.geojson')
    .then(r => r.json())
    .then(data => {
        console.log(data)
    })
 .catch(err => console.error('GeoJSON load error:', err));
```

8. เพิ่ม Geojson ลงในแผนที่
```
map.addSource('districts', {
    type: 'geojson',
    data: geojson
});

map.addLayer({
    id: 'districts-fill',
    type: 'fill',
    source: 'districts',
    paint: {
        'fill-color': '#088',
        'fill-opacity': 0.4
    }
});

map.addLayer({
    id: 'districts-outline',
    type: 'line',
    source: 'districts',
    paint: {
        'line-color': '#000',
        'line-width': 1
    }
});
```

9. เพิ่ม popup ให้กับ layer Geojson
```
map.on('click', 'districts-fill', (e) => {
    const feature = e.features[0];
    const p = feature.properties;
    
    const popupContent = `
        <strong>ชื่อเขต :</strong> ${p.dname || '–'}<br>
        <strong>พื้นที่ :</strong> ${p.area || '–'}<br>
        <strong>รหัสไปรษณีย์ :</strong> ${p.pcode || '–'}<br>
        <strong>จำนวนประชากรผู้ชาย :</strong> ${p.num_male || '–'}<br>
        <strong>จำนวนประชากรผู้หญิง :</strong> ${p.num_female || '–'}<br>
        <strong>จำนวนวัด :</strong> ${p.num_temple || '–'}
    `;

    new maplibregl.Popup()
        .setLngLat(e.lngLat)
        .setHTML(popupContent)
        .addTo(map);
    });

    map.on('mouseenter', 'districts-fill', () => {
        map.getCanvas().style.cursor = 'pointer';
    });

    map.on('mouseleave', 'districts-fill', () => {
        map.getCanvas().style.cursor = '';
    });
};
```

10. เพิ่ม style layer control
```
        .map-overlay {
            position: absolute;
            top: 10px;
            right: 10px;
            background: rgba(255, 255, 255, 0.9);
            padding: 10px;
            border-radius: 5px;
            box-shadow: 0 0 15px rgba(0, 0, 0, 0.2);
            z-index: 1;
        }

        .map-overlay fieldset {
            border: none;
            padding: 0;
            margin: 0;
        }

        .map-overlay input[type="radio"],
        .map-overlay input[type="checkbox"] {
            margin-right: 5px;
        }

        .map-overlay label {
            display: block;
            margin-bottom: 5px;
            cursor: pointer;
        }
```

11. เพิ่ม div map-overlay ใน div map
```
    <div id="map">
        <div class="map-overlay">
            <fieldset>
                <legend>Basemaps</legend>
                <label><input type="radio" name="basemap" value="googleHybrid" checked> Google Hybrid</label>
                <label><input type="radio" name="basemap" value="OSM"> OpenStreetMap</label>
                <label><input type="radio" name="basemap" value="ESRI"> ESRI</label>
                <label><input type="radio" name="basemap" value="cartoLight"> Carto Light</label>
                <label><input type="radio" name="basemap" value="cartoDark"> Carto Dark</label>
            </fieldset>
            <fieldset style="margin-top: 10px;">
                <legend>Layers</legend>
                <label><input type="checkbox" id="toggleMarkers" checked> Markers</label>
                <label><input type="checkbox" id="toggleDistricts" checked> Districts GeoJSON</label>
            </fieldset>
        </div>
    </div>
```
12. เพิ่ม basemapStyles จากนั้นเรียกใช้งานใน map
```
        const basemapStyles = {
            googleHybrid: "./basemap/ghyb.json",
            OSM: "./basemap/osm.json",
            ESRI: "./basemap/esri.json",
            cartoLight: "./basemap/cartoLight.json",
            cartoDark: "./basemap/cartoDark.json"
        };

        const map = new maplibregl.Map({
            container: 'map',
            style: basemapStyles.googleHybrid,
            center: [100.5784086, 13.8455641],
            zoom: 10
        });
```

13. เพิ่มวิธี add marker ด้วย function
```
const currentMarkers = [];
        function addMarkers() {
            markerData.forEach(({ coords, popup }) => {
                const marker = new maplibregl.Marker()
                    .setLngLat(coords)
                    .addTo(map);

                marker.getElement().addEventListener('click', (e) => {
                    e.stopPropagation();
                    new maplibregl.Popup({ offset: 25 })
                        .setLngLat(coords)
                        .setHTML(popup)
                        .addTo(map);
                });
                currentMarkers.push(marker);
            });
        }

        function removeMarkers() {
            currentMarkers.forEach(marker => marker.remove());
            currentMarkers.length = 0;
        }

        function reAddMarkers() {
            removeMarkers();
            if (document.getElementById('toggleMarkers').checked) {
                addMarkers();
            }
        }
```

14. เพิ่ม function  addDistricsLayer
```

let cachedDistrictGeoJSON = null;

        function addDistrictsLayer() {
            const visibilityCheckbox = document.getElementById('toggleDistricts');
            const visibility = visibilityCheckbox.checked ? 'visible' : 'none';

            const addLayers = (geojson) => {
                if (map.getSource('districts')) return;

                map.addSource('districts', {
                    type: 'geojson',
                    data: geojson
                });

                map.addLayer({
                    id: 'districts-fill',
                    type: 'fill',
                    source: 'districts',
                    paint: {
                        'fill-color': '#088',
                        'fill-opacity': 0.4
                    },
                    layout: {
                        'visibility': visibility
                    }
                });

                map.addLayer({
                    id: 'districts-outline',
                    type: 'line',
                    source: 'districts',
                    paint: {
                        'line-color': '#000',
                        'line-width': 1
                    },
                    layout: {
                        'visibility': visibility
                    }
                });

                // Re-attach events
                map.on('click', 'districts-fill', (e) => {
                    const feature = e.features[0];
                    const p = feature.properties;

                    const popupContent = `
                        <strong>ชื่อเขต :</strong> ${p.dname || '–'}<br>
                        <strong>พื้นที่ :</strong> ${p.area || '–'}<br>
                        <strong>รหัสไปรษณีย์ :</strong> ${p.pcode || '–'}<br>
                        <strong>จำนวนประชากรผู้ชาย :</strong> ${p.num_male || '–'}<br>
                        <strong>จำนวนประชากรผู้หญิง :</strong> ${p.num_female || '–'}<br>
                        <strong>จำนวนวัด :</strong> ${p.num_temple || '–'}
                    `;

                    new maplibregl.Popup()
                        .setLngLat(e.lngLat)
                        .setHTML(popupContent)
                        .addTo(map);
                });

                map.on('mouseenter', 'districts-fill', () => {
                    map.getCanvas().style.cursor = 'pointer';
                });
                map.on('mouseleave', 'districts-fill', () => {
                    map.getCanvas().style.cursor = '';
                });
            };

            if (cachedDistrictGeoJSON) {
                addLayers(cachedDistrictGeoJSON);
            } else {
                fetch('./district.geojson')
                    .then(r => r.json())
                    .then(data => {
                        cachedDistrictGeoJSON = data;
                        addLayers(data);
                    })
                    .catch(err => console.error('GeoJSON load error:', err));
            }
        }
```
15. เพิ่ม Event ให้ function Layer Control
```
        map.on('style.load', () => {
            reAddMarkers();
            addDistrictsLayer();
        });

        // Basemap change logic
        document.querySelectorAll('input[name="basemap"]').forEach(radio => {
            radio.addEventListener('change', (event) => {
                const styleName = event.target.value;
                map.setStyle(basemapStyles[styleName]);

                map.once('styledata', () => {
                    reAddMarkers();
                    addDistrictsLayer();
                });
            });
        });

        document.getElementById('toggleMarkers').addEventListener('change', (event) => {
            if (event.target.checked) {
                addMarkers();
            } else {
                removeMarkers();
            }
        });

        document.getElementById('toggleDistricts').addEventListener('change', (event) => {
            const visibility = event.target.checked ? 'visible' : 'none';
            if (map.getLayer('districts-fill')) {
                map.setLayoutProperty('districts-fill', 'visibility', visibility);
            }
            if (map.getLayer('districts-outline')) {
                map.setLayoutProperty('districts-outline', 'visibility', visibility);
            }
        });
```

