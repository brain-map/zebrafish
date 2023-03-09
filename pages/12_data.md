---
layout: page
title: Data
permalink: /data/
feature-img: "assets/img/meshes.png"
tags: [Page]
---

# Access of Data


| Data          | URL |
| -----------   | ----------- |
| image         | gs://neuroglancer/zfish_v1/image |
| segmentation  | gs://neuroglancer/zfish_v1/consensus-20190415   |
| cleft         | gs://neuroglancer/zfish_v1/clefts     |
| skeletons     | https://drive.google.com/drive/folders/1UEb398-fekzqHrhv8JAgszW99Na9aGlK?usp=sharing |
| synapses      | https://drive.google.com/file/d/19R-C1uUHQqwhq6j65AiKk-kWlYkEeaKM/view?usp=sharing |

# Visualization
We use [Neuroglancer](https://github.com/google/neuroglancer) to visualize the image, segmentation, skeleton, and meshes. This is [an example link containing image and segmentation (sparsely proofread)](https://neuromancer-seung-import.appspot.com/#!%7B%22layers%22:%5B%7B%22source%22:%22precomputed://gs://neuroglancer/zfish_v1/image%22%2C%22type%22:%22image%22%2C%22opacity%22:0.43%2C%22blend%22:%22default%22%2C%22shaderControls%22:%7B%7D%2C%22name%22:%22image%22%7D%2C%7B%22source%22:%22precomputed://gs://neuroglancer/zfish_v1/consensus-20190415%22%2C%22type%22:%22segmentation%22%2C%22skeletonRendering%22:%7B%22mode2d%22:%22lines_and_points%22%2C%22mode3d%22:%22lines%22%7D%2C%22name%22:%22consensus-20190415%22%7D%5D%2C%22navigation%22:%7B%22pose%22:%7B%22position%22:%7B%22voxelSize%22:%5B5%2C5%2C45%5D%2C%22voxelCoordinates%22:%5B51461.74609375%2C24880.7734375%2C17072.6640625%5D%7D%7D%2C%22zoomFactor%22:19.889658834717007%7D%2C%22perspectiveOrientation%22:%5B0.33774635195732117%2C-0.7286133766174316%2C-0.27233976125717163%2C0.5299820899963379%5D%2C%22perspectiveZoom%22:2132.77119684805%2C%22showSlices%22:false%2C%22jsonStateServer%22:%22https://www.dynamicannotationframework.com/nglstate/post%22%2C%22selectedLayer%22:%7B%22layer%22:%22consensus-20190415%22%2C%22size%22:460%7D%2C%22layout%22:%224panel%22%7D). 
More example links are in the [Gallery](http://zebrafish.brain-map.github.io/) page. Synapses can also be visualized using Neuroglancer with some customized code.

# Navigation
 Here is a link describing how to navigate once you are on the neuroglancer page [link](https://www.microns-explorer.org/visualization). The Seung lab fork of neuroglancer can be found here [link](https://github.com/seung-lab/neuroglancer).

# Downloading 
For Google Drive links, the data could be downloaded directly by clicking the links.

## Image and Segmentation 
Image and segmentation volumes are stored in Google Cloud Storage. You can cutout some chunks using [CloudVolume](https://github.com/seung-lab/cloud-volume) or [chunkflow](https://github.com/seung-lab/chunkflow)(still using CloudVolume under the hood). For whole dataset downloading, it is recommended to contact us first.

> **_NOTE:_** CloudVolume use f-order (XYZ) while chunkflow use c-order (ZYX) in default!

> **_NOTE:_** https should be used to avoid using credential files.

Example code to cutout an image chunk using CloudVolume:
```python
from cloudvolume import CloudVolume

vol = CloudVolume('precomputed://gs://neuroglancer/zfish_v1/image', use_https=True)
img = vol[51267:51367, 24985:25085, 17072:17172]
```

Example commandline to cutout an image chunk using chunkflow. This command also saves the image chunk to local disk as a HDF5 file and visualize it using Neuroglancer.
```bash
chunkflow load-precomputed -v precomputed://gs://neuroglancer/zfish_v1/image --use-https --chunk-start 17072 24985 51267 --chunk-size 40 400 400 save-h5 -f image.h5 neuroglancer
```

After that you should see a link in the terminal. Click the link, you should see the image volume visualized in webbrowser. 
> **_NOTE:_** If the link is something like http://c02cq0glmd6v.fios-router.home:55676/v/29bd625ab5c364d68ad9f9f37b58a5444d64de56/, try to replace the `c02cq0glmd6v.fios-router.home` with `localhost`.

![](../assets/img/neuroglancer_image_chunk.png)

## Meshes
You can visualize the meshes by clicking the objects in Neuroglancer. 

In order to download the meshes, you can use [CloudVolume](https://github.com/seung-lab/cloud-volume).

The following is an example code modified from CloudVolume documentation:
```python
from cloudvolume import CloudVolume
vol = CloudVolume('gs://mylab/mouse/image', parallel=True, progress=True)
label = 1
mesh = vol.mesh.get(label)

vol.mesh.save(12345) # save 12345 as ./12345.ply on disk
vol.mesh.save([12345, 12346, 12347]) # merge three segments into one file
vol.mesh.save(12345, file_format='obj') # 'ply' and 'obj' are both supported
vol.mesh.get(12345) # return the mesh as vertices and faces instead of writing to disk
vol.mesh.get([ 12345, 12346 ]) # return these two segids fused into a single mesh
vol.mesh.get([ 12345, 12346 ], fuse=False) # return { 12345: mesh, 12346: mesh }
vol.mesh.put(meshes) # works for unsharded legacy only
vol.mesh.delete(segids) # works for unsharded meshes only

mesh.viewer() # Opens GUI. Requires vtk.
```

## Screenshots and Video Recordings
Neuroglancer provides internal tools for [screenshots](https://github.com/google/neuroglancer/blob/master/python/neuroglancer/tool/screenshot.py) and [video recording](https://github.com/google/neuroglancer/blob/master/python/neuroglancer/tool/video_tool.py). The documentation of usage is embedded in the code.

Example usages is as follows:
```
python -m neuroglancer.tool.screenshot --help
python -m neuroglancer.tool.video_tool --help
python -m neuroglancer.tool.screenshot state.json
python -m neuroglancer.tool.video_tool script.txt
```

