# Learnable Region Growing for Point Cloud Segmentation

## Prerequisites

1. numpy
2. scipy
3. scikit-learn
4. tensorflow
5. h5py

## Data Staging

Run the following script to download the necessary point cloud files in H5 format to the *data* folder.

```
bash download_data.sh
```

## Data Visualization

To check the data shape/size contained in each H5 file:

```
python examine_h5.py data/scannet.h5
```

```
<HDF5 dataset "count_room": shape (312,), type "<i4">
<HDF5 dataset "points": shape (7924044, 8), type "<f4">
```

To convert the H5 data file into individual point cloud files (PLY) in format, run the script as follows.
PLY files can be opened using the [CloudCompare](https://www.danielgm.net/cc/) program

```bash
#Render the point clouds in original RGB color
python h5_to_ply.py data/s3dis_area3.h5 --rgb
#Render the point clouds colored according to segmentation ID
python h5_to_ply.py data/s3dis_area3.h5 --seg
```

```
...
Saved to data/viz/22.ply: (18464 points)
Saved to data/viz/23.ply: (20749 points)
```

## Benchmarks

Train benchmark networks such as PointNet and PointNet++ (pointnet2).
```bash
for i in 1 2 3 4 5 6
do
	python -u train_pointnet.py --mode pointnet --area $i >> models/log_pointnet_model$i.txt
done
```

Run benchmark algorithms on each dataset. Mode is one of *normal*, *color*, *pointnet*, *sgpn*, *mcpnet*.

```bash
python benchmarks.py --mode normal --area 1,2,3,4,5,6,scannet --threshold 0.99 --save
```

## Learn Region Grow

Run region growing simulations to stage ground truth data for LrgNet.

```bash
python stage_data.py
```

Train LrgNet for each area of the S3DIS dataset.

```bash
for i in 1 2 3 4 5 6
do
	python train_region_grow.py --area $i
done

Test LrgNet and measure the accuracy metrics.

```bash
python test_region_grow.py --area 1,2,3,4,5,6 --save
```

