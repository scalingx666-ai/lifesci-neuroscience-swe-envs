# Issue

**Title:** Update Zarr Metadata With Existing Multi-Scales Axes

## Description

Addresses:  and 

The zarr metadata was being overwritten with mismatching dimensions. This has the `chunks` dimensions match the axes already specified in the Zarr Metadata.

 Tested using [this dataset ](https://zenodo.org/records/10257532)

1. Run `igneous mesh-forge 20200812-CardiomyocyteDifferentiation14-Cycle1.zarr/B/03/0/labels/nuclei && igneous execute -x -n 8 my-queue`
2.  Try to view the data again, after the mesh forge command has updated the metadata files `igneous view zarr://20200812-CardiomyocyteDifferentiation14-Cycle1.zarr/B/03/0/labels/nuclei`

## Task

Modify the repository so that the issue described above is resolved. The repository is checked out at base commit `ce38856a4d42`. Edit files in place; the verifier captures your changes via `git diff` and scores them against an oracle patch using SWE-RL-style diff-similarity reward.