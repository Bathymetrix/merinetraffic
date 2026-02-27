## merinetraffic
## Tools to interface MERMAIDs with MarineTraffic.com

### _______________________________________
### KML Winnowing for "Custom Area" imports
### _______________________________________

### `winnow_last_50_gps_kml` trims station `position.kml` files to the last 50 unique GPS points

### What It Does
- Reads KML data from `<Folder id="GPS points">`.
- Removes adjacent duplicates where both are identical:
  - datetime from `<name>`
  - latitude/longitude from `<coordinates>`
- Keeps the 50 most recent remaining points.
- Rewrites each point name as:
  - `STATION - YYYY-MM-DD HH:MM`
- Derives station code from the document name (5-character rule), e.g.:
  - `452.120-R-0061` -> `R0061`
  - `452.020-P-24` -> `P0024`

### Usage

#### Single-file mode
Input one KML file:

```bash
python3 winnow_last_50_gps_kml /path/to/position.kml
```

Optional output (`-o`):
- If `-o` is a `.kml` path, writes exactly to that file.
- If `-o` is a directory path, creates it if missing and writes `last_50_gps_<STATION>.kml` inside.

```bash
python3 winnow_last_50_gps_kml /path/to/position.kml -o /path/to/outdir
```

#### Batch mode
Process all station subdirectories under a parent path (`-p`), each containing `position.kml`:

```bash
python3 merinetraffic/winnow_last_50_gps_kml -p /path/to/stations_parent
```

Optional batch output directory (`-o`):
- Creates output directory if missing.
- Writes one file per station as `last_50_gps_<STATION>.kml`.

```bash
python3 merinetraffic/winnow_last_50_gps_kml -p /path/to/stations_parent -o /path/to/outdir
```

### Output Naming
Default output filename per station:
- `last_50_gps_<STATION>.kml`

Example:
- `last_50_gps_R0061.kml`

### Last-Tested Runtime Environment
- OS: Darwin Kernel Version 23.6.0 (`RELEASE_ARM64_T6031`)
- Python: 3.12.4

## Contact
- Joel D. Simon: `jdsimon@bathymetrix.com`

## Attribution
- Script development and iterative modifications were performed in collaboration with Codex (OpenAI).

