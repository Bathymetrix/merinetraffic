# Instructions Log

# _____________________________________________
# `winnow_last_50_gps_kml`
# _____________________________________________

## 2026-02-26

### Environment
- Use Python 3.10.15 by default unless a different version is explicitly recommended.

### KML processing requirements
- Input is a `.kml` file.
- Retain only data from `<Folder id="GPS points">`.
- Work at `<Placemark>` granularity.
- GPS uniqueness rule: two adjacent placemarks are duplicates only if both are equal:
  - parsed datetime from `<name>`
  - lat/lon from `<coordinates>`
- Keep the 50 most recent unique GPS placemarks.
- Convert datetime format in each point `<name>` from `DD/MM/YY HH:MM` to `YYYY-MM-DD HH:MM`.
- Prepend station code and separate with ` - `:
  - target format: `STATION - YYYY-MM-DD HH:MM`
  - example: `R0061 - 2021-05-12 02:17`

### Station code derivation
- Source is the first `<name>` under `<Document>`.
- Use characters after the first `-`.
- Output station code is always 5 characters.
- First and last characters are preserved; zero-pad in the middle as needed.
- Example mappings:
  - `452.120-R-0061` -> `R0061`
  - `452.020-P-24` -> `P0024`

### Completed work
- Created script: `winnow_kml_gps_points.py`
- Generated output: `position_trimmed_50.kml`
- Run summary on provided file:
  - Station code: `R0061`
  - Input GPS placemarks: `6933`
  - After adjacent dedupe: `6810`
  - Written placemarks: `50`

### Workspace note
- Continue appending future chat-derived instructions to this file.

## 2026-02-26 (Batch update)

### Script rename and output naming
- Script renamed to: `winnow_last_50_gps_kml`
- Default output name is now station-specific:
  - `last_50_gps_<STATION>.kml`
  - example: `last_50_gps_R0061.kml`

### Wrapper/batch behavior
- Added directory wrapper mode via `-p`.
- `-p` expects a parent directory containing station subdirectories.
- Each station subdirectory is scanned for `position.kml`.
- For each found file, output is written in that same subdirectory as:
  - `last_50_gps_<STATION>.kml`

### Usage
- Single file:
  - `python3 winnow_last_50_gps_kml /path/to/position.kml`
- Batch mode:
  - `python3 winnow_last_50_gps_kml -p /path/to/parent_dir`

### Validation performed
- Single-file mode validated (output in workspace):
  - `last_50_gps_R0061.kml`
- Batch mode validated on local test parent dir with one station subdir.

## Repo file-rename rule
- This is a git-tracked repo; for renames always try `git mv` first.
- If `git mv` fails, fall back to regular `mv`.

## 2026-02-26 (Python 3.12 optimizations)

### Implemented now
- Replaced full-list sort with `heapq.nlargest(limit, ...)` for selecting the most recent N points.
- Reworked datetime parsing to branch on string shape before parsing, avoiding exception-driven format probing.

### Behavior unchanged
- Output contents and naming rules remain the same.
- Dedupe rule remains adjacent duplicate removal where datetime AND lat/lon both match.

### Validation
- Single-file mode validated with output directory auto-create.
- Batch mode (`-p`) validated with output directory via `-o`.

## 2026-02-27 (follow-up updates)

### Batch summary improvements
- Updated `winnow_last_50_gps_kml` to collect skipped station directories during `-p` runs.
- End-of-run batch output now prints:
  - processed and skipped totals
  - explicit skipped directory list with reason

### Hidden directory handling
- Batch scanning now ignores dot-directories in parent input path (e.g. `.git`, `.cache`, hidden folders).
- Dot-directories are excluded from processing and from skipped counts.

### Output-path behavior retained
- `-o` accepts either:
  - a file path (`.kml`) in single-file mode, or
  - a directory path (auto-created if missing), used in single-file or batch mode.
