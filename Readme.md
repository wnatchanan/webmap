1.เพิ่ม CDN JS/CSS ของ Maplibre ไปยังส่วนของ header ของ Html
```
<!-- Maplibre core CSS/JS -->
<script src="https://unpkg.com/maplibre-gl@^5.6.1/dist/maplibre-gl.js"></script>
<link href="https://unpkg.com/maplibre-gl@^5.6.1/dist/maplibre-gl.css" rel="stylesheet" />
```

2.เพิ่ม style map และ div content id map จากนั้นเรียก script ใช้งาน Maplibre

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


3.เพิ่ม style map และ div content id map จากนั้นเรียก script ใช้งาน Maplibre

```
<div id="map"></div>

const map = new maplibregl.Map({
    container: 'map',
    style: "https://demotiles.maplibre.org/style.json",
    center: [100.5784086, 13.8455641],
    zoom: 5
});
```

4.สร้าง Folder basemap จากนั้น สร้างไฟล์ ghyb.json
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

5.เรียกใช้ไฟล์ ghyb.json ใน script
```
const googleHybrid: "./basemap/ghyb.json"
const map = new maplibregl.Map({
    container: 'map',
    style: googleHybrid,
    center: [100.5784086, 13.8455641],
    zoom: 5
});
```

6.เพิ่ม ตำแหน่ง marker และ ข้อมูล Popup จากนั้นเรียกใช้บนแผนที่
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

7.เรียกใช้งานข้อมูล Geojson จากไฟล์
```
fetch('./district.geojson')
    .then(r => r.json())
    .then(data => {
        console.log(data)
    })
 .catch(err => console.error('GeoJSON load error:', err));
```
