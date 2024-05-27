Karst areas geojson data
========================

The karst areas shown on the community map are loaded from a file in geojson format. This document describes the process of generating this file.

It will likely not be needed for a while as the karst files are not often updated. But should a new version be available, here's how to convert the files.

This procedure was used on a Linux system, but should be easy to adapt for other environements.

## KMZ data

ISSKA has a KMZ file for each continent. These are gzipped [KML files](https://en.wikipedia.org/wiki/Keyhole_Markup_Language)

Download and extract each file:

```
unzip Africa_Karst.kmz -d Africa
unzip Asian_Karst.kmz -d Asian
unzip Australasian_Karst.kmz -d Australasian
unzip Europe_Karst.kmz -d Europe
unzip North_American_Karst.kmz -d North_American
unzip South_American_Karst.kmz -d South_American
```

This will extract each file in a different folder. In that folder you'll find a file named `doc.kml` and sometimes an `.xsl` file. The `.kml` file is the one with the data that we need.

## Convert to geojson

To convert the kml data to geojson we use [togeojson-cli](https://github.com/tmcw/togeojson-cli), which is a Node program that does just that. Either install `togeojson-cli` globally with npm, or if we already have [npx](https://www.npmjs.com/package/npx) on the system we can just execute it without actually installing it:

(to be run in the folder where the `kmz` files were downloaded)

```
npx @tmcw/togeojson-cli Africa/doc.kml > Africa.json
npx @tmcw/togeojson-cli Asian/doc.kml > Asian.json
npx @tmcw/togeojson-cli Australasian/doc.kml > Australasian.json
npx @tmcw/togeojson-cli Europe/doc.kml > Europe.json
npx @tmcw/togeojson-cli North_American/doc.kml > North_American.json
npx @tmcw/togeojson-cli South_American/doc.kml > South_American.json
```

This will create a geojson file for each continent.

## Create a single file and remove unnecessary data

To put everything in a single file, you can create this javascript Node file in the folder where the `kmz` files were downloaded:

```js
const fs = require("fs");

const files = [
  "Africa",
  "Asian",
  "Australasian",
  "Europe",
  "North_American",
  "South_American",
];

let features = [];

files.forEach(file => {
  // Get the features of each file and put them in a single array
  const data = JSON.parse(fs.readFileSync(file + ".json", "utf8"));
  features = features.concat(data.features.map(feature => {
    // Remove name, HTML description and color information
    delete feature.properties;
    return feature;
  }));
  }));
});

// Export to JSON
// We wrap everything in a featurecollection, like it was done in the original files (maybe it's not absolutely needed)
// The replace call puts every feature (which maps to a country) on a new line
fs.writeFileSync("karst_areas.geojson", JSON.stringify({
  type: "FeatureCollection",
  features,
}).replace(/\{"type":"Feature"/g, '\n{"type":"Feature"'));
```

Then run it as `node filename.js`. This will create a `karst_areas.geojson` file that you can copy to the project source code.

