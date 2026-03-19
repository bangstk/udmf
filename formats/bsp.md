# Binary BSP tree formats
The "binary space partition" tree is the core data structure used in Doom's [software rendering engine](https://doomwiki.org/wiki/Doom_rendering_engine).
Compiled after the mapper-facing data for the level has already been put together (i.e. Things, vertices, lines, sides and sectors).
It is comprised of Nodes, SubSectors and Segments -- the renderer recursively traversees the Nodes tree, until encountering a SubSector, then drawing its associated Segments.
Doom uses "-1" (either 16bit `0xFFFF` or 32bit `0xFFFFFFFF`) to represent the lack of an index in index types -- henceforth `NO_INDEX`.

| BSP tree type | Lump magic header   | Lump where header is found |
|---------------|---------------------|----------------------------|
| Vanilla       |                     |                            |
| DeepBSPV4     | "`xNd4\0\0\0\0`"    | `NODES`                    |
| XNOD          | "`XNOD`"            | `NODES`                    |
| XGLN          | "`XGLN`"            | `SSECTORS`                 |
| XGL2          | "`XGL2`"            | `SSECTORS`                 |
| XGL3          | "`XGL3`"            | `SSECTORS`                 |

## Vanilla, DoomBSP structures
These are the data structure definitions associated with the vanilla BSP tree implementation.

### Bounding box (`bbox_t`), 8 bytes
| Offset | Size | C99 Type  | Code             | Description |
|--------|------|-----------|------------------|-------------|
| 0      | 2    | `int16_t` | `box[BOXTOP]`    | Maximum Y coordinate |
| 2      | 2    | `int16_t` | `box[BOXBOTTOM]` | Minimum Y coordinate |
| 4      | 2    | `int16_t` | `box[BOXLEFT]`   | Minimum X coordinate |
| 6      | 2    | `int16_t` | `box[BOXRIGHT]`  | Maximum X coordinate |

### Vanilla node (`node_doom_t`), 28 bytes
| Offset | Size | C99 Type   | Code          | Description |
|--------|------|------------|---------------|-------------|
| 0      | 2    | `int16_t`  | `x`           | Starting point X coordinate |
| 2      | 2    | `int16_t`  | `y`           | Starting point Y coordinate |
| 4      | 2    | `int16_t`  | `dx`          | Delta from X to ending point |
| 6      | 2    | `int16_t`  | `dy`          | Delta from Y to ending point |
| 8      | 8    | `bbox_t`   | `bbox[0][4]`  | Bounding box on right side |
| 16     | 8    | `bbox_t`   | `bbox[1][4]`  | Bounding box on left side |
| 24     | 2    | `uint16_t` | `children[0]` | Child node index for right side -- if highest bit is set, is a subsector |
| 26     | 2    | `uint16_t` | `children[1]` | Child node index for left side -- if highest bit is set, is a subsector |

### Vanilla subsector (`subsec_doom_t`), 4 bytes
| Offset | Size | C99 Type   | Code     | Description |
|--------|------|------------|----------|-------------|
| 0      | 2    | `uint16_t` | numsegs  | Number of segments in this subsector |
| 2      | 2    | `uint16_t` | firstseg | Index of first segment |

### Vanilla segment (`seg_doom_t`), 12 bytes
| Offset | Size | C99 Type   | Code      | Description |
|--------|------|------------|-----------|-------------|
| 0      | 2    | `uint16_t` | `v1`      | Index of starting vertex |
| 2      | 2    | `uint16_t` | `v2`      | Index of ending vertex |
| 4      | 2    | `uint16_t` | `angle`   | BAM angle, 0 is east, 16384 is north, and so on |
| 6      | 2    | `uint16_t` | `linedef` | Index of associated linedef |
| 8      | 2    | `uint16_t` | `side`    | False (0) if on the front side of a linedef, true (1) if opposite side |
| 10     | 2    | `uint16_t` | `offset`  | Offset from starting point to ending point |

## DeepSea BSP, DeepBSPV4, structures
These are the data structure definitions associated with the DeepSea editor's BSP format extension.
The sole difference from the vanilla BSP types, is that the types referring to indexes are now 32bit instead of 16bit, but are otherwise identical.
Pulled from the official [DeepBSPV4 webpage spec](https://www.sbsoftware.com/files/DeePBSPV4specs.txt).

### DeepBSPV4 node (`node_deep_t`), 32 bytes
| Offset | Size | C99 Type   | Description |
|--------|------|------------|-------------|
| 0      | 2    | `int16_t`  | Starting point X coordinate |
| 2      | 2    | `int16_t`  | Starting point Y coordinate |
| 4      | 2    | `int16_t`  | Delta from X to ending point |
| 6      | 2    | `int16_t`  | Delta from Y to ending point |
| 8      | 8    | `bbox_t`   | Bounding box on right side |
| 16     | 8    | `bbox_t`   | Bounding box on left side |
| 24     | 4    | `uint32_t` | Child node index for right side -- if highest bit is set, is a subsector |
| 28     | 4    | `uint32_t` | Child node index for left side -- if highest bit is set, is a subsector |

### DeepBSPV4 subsector (`subsec_deep_t`), 6 bytes
| Offset | Size | C99 Type   | Description |
|--------|------|------------|-------------|
| 0      | 2    | `uint16_t` | Number of segments in this subsector |
| 2      | 4    | `uint32_t` | Index of first segment |

### DeepBSPV4 segment (`seg_deep_t`), 16 bytes
| Offset | Size | C99 Type   | Description |
|--------|------|------------|-------------|
| 0      | 4    | `uint32_t` | Index of starting vertex |
| 4      | 4    | `uint32_t` | Index of ending vertex |
| 8      | 2    | `uint16_t` | BAM angle, 0 is east, 16384 is north, and so on |
| 10     | 2    | `uint16_t` | Index of associated linedef |
| 12     | 2    | `uint16_t` | False (0) if on the front side of a linedef, true (1) if opposite side |
| 14     | 2    | `uint16_t` | Offset from starting point to ending point |

## ZDoom extended BSP structures
Pulled from the ZDoom wiki page on the [extended Node formats](https://zdoom.org/w/index.php?title=Node#ZDoom_extended_nodes).
Differences from the vanilla BSP data include:
* the use of 32bit indexes
* storing all data in a specific sequence on a single lump, as opposed to using multiple lumps
 * in binary map formats, `NODES` for XNOD, and `SSECTOR` for XGLN/XGL2/XGL3. in UDMF, always `ZNODES`.
* each version is built on top of the previous, replacing the predecessor's given struct type
* the removal of angles and offsets from Segments
* higher precision data types for fractional coordinates

### XNOD vertex (`vertex_xnod_t`), 8 bytes
| Offset | Size | C99 Type  | Description |
|--------|------|-----------|-------------|
| 0      | 4    | `int32_t` | Fixed point X coordinate |
| 4      | 4    | `int32_t` | Fixed point Y coordinate |

### XNOD node (`node_xnod_t`), 32 bytes
| Offset | Size | C99 Type   | Description |
|--------|------|------------|-------------|
| 0      | 2    | `int16_t`  | Starting point X coordinate |
| 2      | 2    | `int16_t`  | Starting point Y coordinate |
| 4      | 2    | `int16_t`  | Delta from X to ending point |
| 6      | 2    | `int16_t`  | Delta from Y to ending point |
| 8      | 8    | `bbox_t`   | Bounding box on right side |
| 16     | 8    | `bbox_t`   | Bounding box on left side |
| 24     | 4    | `uint32_t` | Child node index for right side -- if highest bit is set, is a subsector |
| 28     | 4    | `uint32_t` | Child node index for left side -- if highest bit is set, is a subsector |

### XNOD subsector (`subsec_xnod_t`), 4 bytes
| Offset | Size | C99 Type   | Description |
|--------|------|------------|-------------|
| 0      | 4    | `uint32_t` | Number of segments in this subsector |

### XNOD segment (`seg_xnod_t`), 11 bytes
| Offset | Size | C99 Type   | Description |
|--------|------|------------|-------------|
| 0      | 4    | `uint32_t` | Index of starting vertex |
| 4      | 4    | `uint32_t` | Index of ending vertex |
| 8      | 2    | `uint16_t` | Index of associated linedef |
| 10     | 1    | `uint8_t`  | False (0) if on the front side of a linedef, true (1) if opposite side |

### XGLN segment (`seg_xgln_t`), 11 bytes
Starting from the XGLN format, and applying on the following "XGL" formats, the following points are now required.
* "minisegs", indicated by `NO_INDEX`, to be skipped by the software renderer, but needed for hardware renderers
* Segments sorted by clockwise angle on Subsectors
* due to the Segment sorting, only the first Vertex is require

| Offset | Size | C99 Type   | Description |
|--------|------|------------|-------------|
| 0      | 4    | `uint32_t` | Index of starting vertex |
| 4      | 4    | `uint32_t` | Index of partner segment (Unused in most ports outside of U/G/ZDoom) |
| 8      | 2    | `uint16_t` | Index of associated linedef, `NO_INDEX` if mini-seg |
| 10     | 1    | `uint8_t`  | False (0) if on the front side of a linedef, true (1) if opposite side |

### XGL2 segment (`seg_xgl2_t`), 13 bytes
| Offset | Size | C99 Type   | Description |
|--------|------|------------|-------------|
| 0      | 4    | `uint32_t` | Index of starting vertex |
| 4      | 4    | `uint32_t` | Index of partner segment (Unused in most ports outside of U/G/ZDoom) |
| 8      | 4    | `uint32_t` | Index of associated linedef, `NO_INDEX` if mini-seg |
| 12     | 1    | `uint8_t`  | False (0) if on the front side of a linedef, true (1) if opposite side |

### XGL3 node (`node_xgl3_t`), 40 bytes
| Offset | Size | C99 Type   | Description |
|--------|------|------------|-------------|
| 0      | 4    | `int32_t`  | Starting point X coordinate |
| 4      | 4    | `int32_t`  | Starting point Y coordinate |
| 8      | 4    | `int32_t`  | Delta from X to ending point |
| 12     | 4    | `int32_t`  | Delta from Y to ending point |
| 16     | 8    | `bbox_t`   | Bounding box on right side |
| 24     | 8    | `bbox_t`   | Bounding box on left side |
| 32     | 4    | `uint32_t` | Child node index for right side -- if highest bit is set, is a subsector |
| 36     | 4    | `uint32_t` | Child node index for left side -- if highest bit is set, is a subsector |

### ZDoom BSP structure
The binary reading for all ZDoom formats can be done in a single procedure/function, as long as the proper data types are passed correctly.

#### XNOD sequence
| Size                 | Type            | Description |
|----------------------|-----------------|-------------|
| 4                    | `char[4]`       | Header string, i.e "XNOD" |
| 4                    | `uint32_t`      | Number of old, i.e. original, vertices |
| 4                    | `uint32_t`      | Number of new, i.e. BSP-generated, vertices |
| 8 * new vertex count | `vertex_xnod_t` | All BSP-generated vertices |
| 4                    | `uint32_t`      | Number of subsectors |
| 4 * subsector count  | `subsec_xnod_t` | All subsectors |
| 4                    | `uint32_t`      | Number of segments |
| 11 * segment count   | `seg_xnod_t`    | All segments |
| 4                    | `uint32_t`      | Number of nodes |
| 32 * segment count   | `node_xnod_t`   | All segments |

#### XGLN sequence
| Size                 | Type            | Description |
|----------------------|-----------------|-------------|
| 4                    | `char[4]`       | Header string, i.e "XGLN" |
| 4                    | `uint32_t`      | Number of old, i.e. original, vertices |
| 4                    | `uint32_t`      | Number of new, i.e. BSP-generated, vertices |
| 8 * new vertex count | `vertex_xnod_t` | All BSP-generated vertices |
| 4                    | `uint32_t`      | Number of subsectors |
| 4 * subsector count  | `subsec_xnod_t` | All subsectors |
| 4                    | `uint32_t`      | Number of segments |
| 11 * segment count   | `seg_xgln_t`    | All segments |
| 4                    | `uint32_t`      | Number of nodes |
| 32 * segment count   | `node_xnod_t`   | All segments |

#### XGL2 sequence
| Size                 | Type            | Description |
|----------------------|-----------------|-------------|
| 4                    | `char[4]`       | Header string, i.e "XGL2" |
| 4                    | `uint32_t`      | Number of old, i.e. original, vertices |
| 4                    | `uint32_t`      | Number of new, i.e. BSP-generated, vertices |
| 8 * new vertex count | `vertex_xnod_t` | All BSP-generated vertices |
| 4                    | `uint32_t`      | Number of subsectors |
| 4 * subsector count  | `subsec_xnod_t` | All subsectors |
| 4                    | `uint32_t`      | Number of segments |
| 13 * segment count   | `seg_xgl2_t`    | All segments |
| 4                    | `uint32_t`      | Number of nodes |
| 32 * segment count   | `node_xnod_t`   | All segments |

#### XGL3 sequence
| Size                 | Type            | Description |
|----------------------|-----------------|-------------|
| 4                    | `char[4]`       | Header string, i.e "XGL3" |
| 4                    | `uint32_t`      | Number of old, i.e. original, vertices |
| 4                    | `uint32_t`      | Number of new, i.e. BSP-generated, vertices |
| 8 * new vertex count | `vertex_xnod_t` | All BSP-generated vertices |
| 4                    | `uint32_t`      | Number of subsectors |
| 4 * subsector count  | `subsec_xnod_t` | All subsectors |
| 4                    | `uint32_t`      | Number of segments |
| 12 * segment count   | `seg_xgl2_t`    | All segments |
| 4                    | `uint32_t`      | Number of nodes |
| 40 * segment count   | `node_xgl3_t`   | All segments |

## LEAFS structure
An additional lump used in the PlayStation ports of Doom, Final Doom and Hexen, as well as Doom 64.
Must have all entries clockwise-sorted in each subsector.
Each `leaf_t` matches one-to-one with the list of subsectors.

| Offset | Size           | Type     | Description |
|--------|----------------|----------|-------------|
| 0      | 2              | uint16_t | Number of references in this `leaf_t`. |
| 2      | 4 * references | leaf_t   | References to seg-vertex index pairs. |

| Offset | Size | Type     | Description |
|--------|------|----------|-------------|
| 0      | 2    | uint16_t | Vertex index. |
| 2      | 2    | uint16_t | Segment index. |

## FLOORs structure
An additional lump used in the Nintendo 64 port of Hexen. Though not part, or an extension, of the BSP tree itself, it is used in the rendering process.
Each `floor_t` matches one-to-one with the list of sectors.

| Offset | Size          | Type     | Description |
|--------|---------------|----------|-------------|
| 0      | 2             | uint16_t | Number of triangles in this `floor_t`. |
| 2      | 6 * triangles | leaf_t   | Trio of vertex indices forming a `triangle_t`. |

| Offset | Size | Type     | Description |
|--------|------|----------|-------------|
| 0      | 2    | uint16_t | First vertex index. |
| 2      | 2    | uint16_t | Second vertex index. |
| 4      | 2    | uint16_t | Third vertex index. |
