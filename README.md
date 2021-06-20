# PyEllCV

----

## Warning
<span style="color:red">**The code has been moved to: [https://gitlab.inria.fr/tangram/pyellcv](https://gitlab.inria.fr/tangram/pyellcv)**</span>

----

This repository regroups tools to manipulate ellipses and ellipsoids in the context of camera pose estimation. Most of the code is in python, but some parts were written in C++ for efficiency purposes.

This library is used in combination with 3D-aware predicted ellipses for object-based visual localization in [3D-Aware-Ellipses-for-Visual-Localization](https://gitlab.inria.fr/tangram/3D-Aware-Ellipses-for-Visual-Localization)

## Installation

The library can be build with the following commands:

```
python -m pip install 'git+https://gitlab.inria.fr/mzins/pyellcv.git'
# (add --user if you don't have permission)

# Or, to install it from a local clone:
git clone --recursive  https://gitlab.inria.fr/mzins/pyellcv.git
python -m pip install -e ./pyellcv
```

## Example


```python
import cv2
import numpy as np
from ellcv.types import Ellipsoid
from ellcv.utils import generate_random_scene, generate_random_camera, \
                        generate_K, bbox_from_ellipse
from ellcv.visu import draw_ellipse, draw_bbox


# Create a scene of ellipsoids
N = 4
scene = generate_random_scene(N, axes_range=[0.3, 1.5], position_range=[-3.0, 3.0])

# Define the camera
W, H = 640, 480
o, p = generate_random_camera(dist_range=[10.0, 15.0], target_range=[-0.5, 0.5])
K = generate_K(500, W/2, H/2)
P = K @ np.hstack((o.T, -o.T@p.reshape((3, 1))))

# Project and display
img = np.zeros((H, W, 3), dtype=np.uint8)
for ell in scene:
    ellipse = ell.project(P)
    bbox = bbox_from_ellipse(ellipse)
    draw_bbox(img, bbox, color=(255, 0, 0))
    draw_ellipse(img, ellipse, color=(0, 255, 0))
cv2.imshow("viz", img)
cv2.waitKey()
```



## References

- The code for p3p originally comes from [RPG Monocular Pose Estimator](https://github.com/uzh-rpg/rpg_monocular_pose_estimator).
- The code for ellipses Intersection-over-Union computations comes from [EEOver](https://github.com/chraibi/EEOver).
