1️⃣ What GEOADD does internally
GEOADD drivers 77.5946 12.9716 driver:123


Redis takes the latitude and longitude and encodes them into a 52-bit integer geohash for fast spatial indexing.

But it does not discard the original coordinates.

The full lat/lon (floating point) is still associated with the member internally.

The geohash is just for efficient lookup in the sorted set.

So effectively, each driver is stored as:

member: driver:123
geoindex: 52-bit geohash integer
lat: 12.9716
lon: 77.5946


Redis exposes the geohash only internally; the lat/lon are preserved.

2️⃣ How distance is calculated

When you call:

GEODIST drivers driver:123 driver:456 km


Redis fetches the stored lat/lon for both members.

Applies the haversine (great-circle) formula internally.

Returns the exact distance in km (or miles).

✅ You don’t have to manually compute anything — the geohash is just for fast candidate lookup, not for distance computation.

3️⃣ How GEOSEARCH / GEORADIUS works

Internally, Redis uses geohash boxes to quickly filter candidates within a radius.

Then, for the final set, it computes the exact distance using the stored lat/lon.

If you specify ASC, results are already returned in increasing distance order.

Summary
Concept	Stored	Purpose
Geohash (52-bit)	Yes	Fast spatial indexing & filtering
Lat/Lon	Yes	Exact distance calculation (haversine)
GEODIST / GEOSEARCH	N/A	Uses stored lat/lon internally for precise results
