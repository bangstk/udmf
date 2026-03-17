# Binary map formats

## Doom
The original.

### Doom lump order
* Map marker
* `THINGS`
* `LINEDEFS`
* `SIDEDEFS`
* `VERTEXES`
* `SEGS`
* `SSECTORS`
* `NODES`
* `SECTORS`
* `REJECT`
* `BLOCKMAP`

### Vertices, (`vertex_doom_t`) 4 bytes
| Offset | Size | C99 type   | Code | Description |
|--------|------|------------|------|-------------|
| 0      | 2    | `int16_t`  | `x`  | Coordinate's X component, non-fractional fixed point |
| 2      | 2    | `int16_t`  | `y`  | Coordinate's Y component, non-fractional fixed point |

### Linedefs, (`linedef_doom_t`) 14 bytes
| Offset | Size | C99 type   | Code         | Description |
|--------|------|------------|--------------|-------------|
| 0      | 2    | `int16_t`  | `v1`         | Starting vertex index |
| 2      | 2    | `int16_t`  | `v2`         | Ending vertex index |
| 4      | 2    | `int16_t`  | `flags`      | Line flags |
| 6      | 2    | `int16_t`  | `special`    | Special action when line is activated |
| 8      | 2    | `int16_t`  | `tag`        | Special action target sector/line |
| 10     | 2    | `int16_t`  | `sidenum[0]` | Front side index |
| 12     | 2    | `int16_t`  | `sidenum[1]` | Back side index, will be -1 if one-sided |

### Sidedefs, (`sidedef_doom_t`) 30 bytes
| Offset | Size | C99 type   | Code            | Description |
|--------|------|------------|-----------------|-------------|
| 0      | 2    | `int16_t`  | `textureoffset` | Horizontal offset, applies to all three textures |
| 2      | 2    | `int16_t`  | `rowoffset`     | Vertical offset, applies to all three textures |
| 4      | 8    | `char[8]`  | `toptexture`    | Upper texture name |
| 12     | 8    | `char[8]`  | `bottomtexture` | Lower texture name |
| 20     | 8    | `char[8]`  | `midtexture`    | Middle texture name |
| 28     | 2    | `int16_t`  | `sector`        | Sidedef's facing sector |

### Sectors, (`sector_doom_t`) 26 bytes
| Offset | Size | C99 type   | Code            | Description |
|--------|------|------------|-----------------|-------------|
| 0      | 2    | `int16_t`  | `floorheight`   | Sector's floor height, non-fractional fixed point |
| 2      | 2    | `int16_t`  | `ceilingheight` | Sector's ceiling height, non-fractional fixed point |
| 10     | 8    | `char[8]`  | `floorpic`      | Sector's floor flat name |
| 18     | 8    | `char[8]`  | `ceilingpic`    | Sector's ceiling flat name |
| 20     | 2    | `int16_t`  | `lightlevel`    | Default brightness level |
| 22     | 2    | `int16_t`  | `special`       | Special action triggered when Thing steps on sector |
| 24     | 2    | `int16_t`  | `tag`           | Target for lines' special action |

### Things, (`thing_doom_t`) 10 bytes
| Offset | Size | C99 type   | Code      | Description |
|--------|------|------------|-----------|-------------|
| 0      | 2    | `int16_t`  | `x`       | Spawn point X coordinate |
| 2      | 2    | `int16_t`  | `y`       | Spawn point Y coordinate |
| 4      | 2    | `int16_t`  | `angle`   | Spawn facing angle |
| 6      | 2    | `int16_t`  | `type`    | Editor number |
| 8      | 2    | `int16_t`  | `options` | Spawn flags |

## Hexen
Uses the previous Doom definitions as a base, with the following changed structures.

### Hexen lump order
* Map marker
* `THINGS`
* `LINEDEFS`
* `SIDEDEFS`
* `VERTEXES`
* `SEGS`
* `SSECTORS`
* `NODES`
* `SECTORS`
* `REJECT`
* `BLOCKMAP`
* `BEHAVIOR`

### Linedefs, (`linedef_hexen_t`) 16 bytes
| Offset | Size | C99 type   | Code         | Description |
|--------|------|------------|--------------|-------------|
| 0      | 2    | `int16_t`  | `v1`         | Starting vertex index |
| 2      | 2    | `int16_t`  | `v2`         | Ending vertex index |
| 4      | 2    | `int16_t`  | `flags`      | Line flags |
| 6      | 1    | `uint8_t`  | `special`    | Special action when line is activated |
| 7      | 1    | `uint8_t`  | `arg1`       | Special action argument 1 |
| 8      | 1    | `uint8_t`  | `arg2`       | Special action argument 2 |
| 9      | 1    | `uint8_t`  | `arg3`       | Special action argument 3 |
| 10     | 1    | `uint8_t`  | `arg4`       | Special action argument 4 |
| 11     | 1    | `uint8_t`  | `arg5`       | Special action argument 5 |
| 12     | 2    | `int16_t`  | `sidenum[0]` | Front side index |
| 14     | 2    | `int16_t`  | `sidenum[1]` | Back side index, will be -1 if one-sided |

### Things, (`thing_hexen_t`) 20 bytes
| Offset | Size | C99 type   | Code      | Description |
|--------|------|------------|-----------|-------------|
| 0      | 2    | `int16_t`  | `tid`     | Scripting ID |
| 2      | 2    | `int16_t`  | `x`       | Spawn point X coordinate |
| 4      | 2    | `int16_t`  | `y`       | Spawn point Y coordinate |
| 6      | 2    | `int16_t`  | `height`  | Spawn point vertical position, relative to ceiling if MF_SPAWNCEILING is set |
| 8      | 2    | `int16_t`  | `angle`   | Spawn facing angle |
| 10     | 2    | `int16_t`  | `type`    | Editor number |
| 12     | 2    | `int16_t`  | `options` | Spawn flags |
| 14     | 1    | `uint8_t`  | `special` | Special action when triggered |
| 15     | 1    | `uint8_t`  | `arg1`    | Special action argument 1 |
| 16     | 1    | `uint8_t`  | `arg2`    | Special action argument 2 |
| 17     | 1    | `uint8_t`  | `arg3`    | Special action argument 3 |
| 18     | 1    | `uint8_t`  | `arg4`    | Special action argument 4 |
| 19     | 1    | `uint8_t`  | `arg5`    | Special action argument 5 |
