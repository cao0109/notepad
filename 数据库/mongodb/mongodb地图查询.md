# mongodb地图查询

(1) 2dsphere ：此索引类型支持查询地球球体上的位置，支持"GEOJSON"和专统坐标类型
的数据。
* GeoJSON数据：需要使用嵌入式文档。在其中可以通过“coordinates”字段来指定坐标位置，以及可以通过“type”字段来指定坐标的类型。type可以分3中形式

点（Point）：若“type”为“Point”，则“coordinates”字段只有一个坐标

线（LineString）：若“type”为“LineString”，则“coordinates”字段会有两个坐标

多边形（Polygon）：若“type”为“Polygon”，则“coordinates”字段会有两个以上的坐标

* 传统坐标数据：只需要一个字段指定坐标的位置

无论是GeoJSON 数据还是传统坐标数据，真中经纬度的存储方式必须是数组形式，即［经度，纬度］，且经纬度必须有效，如果经纬度的存储位置颠倒，或者无效，创建时会报错

(2) 2d：此索引类型支持查询二维平面上的位置，仅支持传统坐标类型的数据。

```sql
//范例1 在GeoJSON类型的文档中使用2dsphere 类型的索引
db.getCollection("Warehouse").insertOne({
    SysNo: 1001,
    name: "东软",
    address: "沈阳市浑南区新秀街(东软软件园)",
    location: {
        type: "Point",
        coordinates: [123.43616, 41.70877]
    }
});

db.Warehouse.createIndex({location:"2dsphere"});

db.Warehouse.aggregate(
    [{
        $geoNear: { //$geoNear是一个高级查询，它可以用来查询地理位置信息。
            near: { //near是一个对象，它指定了查询的中心点。
                type: "Point", //type是一个字符串，它指定了查询的中心点的类型。
                coordinates: [123.450991, 41.717795] //coordinates是一个数组，它指定了查询的中心点的坐标。
            },
            distanceField: "dist.location", //distanceField是一个字符串，它指定了计算距离的字段名称。 这个字段将会被设置为一个数字，表示距离中心点的距离。 单位是米。
            key: "location", //key是一个字符串，它指定了查询的索引名称。
            spherical: true, //spherical是一个布尔值，它指定了是否使用球面距离。
            maxDistance: 2000, //maxDistance是一个数字，它指定了查询的最大距离。 单位是米。
            query: { //query是一个对象，它指定了查询的条件。
                SysNo: { $gt: 1000 } //SysNo是一个数字，它指定了查询的条件。
            },
            includeLocations: "dist.location", //includeLocations是一个字符串，它指定了包含的字段名称。
            spherical: true, //spherical是一个布尔值，它指定了是否使用球面距离。
            distanceMultiplier: 0.001 //distanceMultiplier是一个数字，它指定了距离的倍数。
        }
    }
    ]
);
```

```sql
//范例2：在传统坐标类型的数据中使用“2d”类型的索引
db.getCollection("Warehouse_2").insertOne({
    SysNo: 1021,
    name: "东软",
    address: "沈阳市浑南区新秀街(东软软件园)",
    coordinates: [123.436716, 41.706877]
});

db.Warehouse_2.createIndex({"coordinates":"2d"});

db.Warehouse_2.aggregate(
    [{
        $geoNear: {
            near: [123.450991, 41.717795]
            ,
            distanceField: "dist.location",
            key: "coordinates"
        }
    }]
);
```

