# LINDI - Linked Data Interface

warning: Please note, LINDI is currently under development and not yet operational.

LINDI is a Python library that facilitates handling NWB (Neurodata Without Borders) files in an efficient, flexible manner that minimizes the need for excessive data downloads. Our goal is to enable composition of NWB files by integrating data from multiple sources.

Key features include:

- A read-only [Zarr store](https://zarr.readthedocs.io/en/stable/) for hdf5 files, including NWB (in progress)
- Generation of a relatively small JSON file representing the NWB Zarr store, inspired by [kerchunk](https://github.com/fsspec/kerchunk).
- An [h5py](https://www.h5py.org/)-like interface for accessing NWB Zarr stores that can be used with [pynwb](https://pynwb.readthedocs.io/en/stable/), although this feature is not yet functional.
- The ability to assemble composite NWB files that draw from multiple sources, which is also not yet functional.

This project was inspired by [kerchunk](https://github.com/fsspec/kerchunk) and [hdmf-zarr](https://hdmf-zarr.readthedocs.io/en/latest/index.html).

## Installation

Clone this repo and then

```bash
cd lindi
pip install -e .
```

## Example usage

```python
import lindi
import pynwb

# Here's an example DANDI NWB file

# https://neurosift.app/?p=/nwb&dandisetId=000717&dandisetVersion=draft&url=https://api.dandiarchive.org/api/assets/3d12a902-139a-4c1a-8fd0-0a7faf2fb223/download/
h5_url = "https://api.dandiarchive.org/api/assets/3d12a902-139a-4c1a-8fd0-0a7faf2fb223/download/"


# Create the read-only Zarr store for the h5 file
store = lindi.LindiH5Store.from_file(h5_url)

# Create the reference file system object
rfs = store.to_reference_file_system()

# For reference, save it to a file
with open("example.zarr.json", "w") as f:
    json.dump(rfs, f, indent=2)

# Create the client from the reference file system
client = lindi.LindiClient.from_reference_file_system(rfs)

# Try to read using pynwb
# (This part does not work yet)
with pynwb.NWBHDF5IO(file=client, mode="r") as io:
    nwbfile = io.read()
    print(nwbfile)
```

## License

See [LICENSE](LICENSE).
