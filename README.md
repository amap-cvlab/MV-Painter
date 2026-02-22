# MVPainter
<div align="left">
  <a href=https://amap-cvlab.github.io/MV-Painter/ target="_blank"><img src=https://img.shields.io/badge/Project%20Page-333399.svg?logo=homepage height=22px></a>
 <a href='https://huggingface.co/shaomq/MVPainter'><img src='https://img.shields.io/badge/%F0%9F%A4%97%20Hugging%20Face-Model-blue'></a> &nbsp;
 <a href='https://arxiv.org/pdf/2505.12635'><img src='https://img.shields.io/badge/arXiv-Report-b31b1b.svg'></a> &nbsp;

</div>
MVPainter is a fully open-source system for 3D texture generation, providing a comprehensive solution including data, models, and evaluation tools.

<img src="./assets/teaser.png" alt="Pipeline" width="800"/>


- [ ] Release gradio demo.
- [x] Release technical report.
- [x] Release training code.
- [x] Release data processing code.
- [x] Fixed Blender GLTF import issues and NaN handling

## ✨Key Features
* 🔓 Fully Open-Source Texture Generation Pipeline

* 🧩 Compatible with Mainstream 3D Geometry Generation Methods

* 🎨 High-Fidelity, Geometry- and Image-Aligned PBR Texture Generation

## 🔧 Important Fixes Applied

### Blender GLTF Import Fix
Fixed the `TypeError: bpy_struct: item.attr = val: IMPORT_SCENE_OT_gltf.loglevel expected an int type, not NoneType` error by:
- Adding explicit `loglevel=0` parameter to all `bpy.ops.import_scene.gltf()` calls
- Implementing comprehensive error handling with fallback attempts
- Updated files: `scripts/blender_render_ortho.py`, `evaluation/blender_render_eval.py`, `scripts/blender_bake.py`

### NaN Value Handling
Fixed the `ValueError: cannot convert float NaN to integer` error by:
- Adding NaN/Inf validation for UV coordinates and vertex positions
- Implementing graceful fallbacks when invalid values are detected
- Added mesh validation in texture generation pipeline
- Updated files: `mesh_processor.py`, `generation_endpoints.py`

### Blender Version Support
- Supports both Blender 4.2.4 and 4.2.9
- Automatic symlink creation for expected Blender paths
- Backward compatibility maintained

## 🔥 News
* [2025-01-06] Fixed critical Blender GLTF import and NaN handling issues
* [2025-04-30] We have released our [technical report](https://huggingface.co/shaomq/MVPainter)!
* [2025-04-30] We have released model weights, data processing, trainning, inference scripts.

## 🔧[Installation](./MVPainter/INSTALL.md)
Installation guides can be found in [INSTALL.md](./MVPainter/INSTALL.md).

**Important**: After installation, ensure you have proper Blender setup:

```bash
# Verify Blender installation
ls blender-4.2.4-linux-x64/blender  # Should exist
# OR
ls blender-4.2.9-linux-x64/blender  # Should exist

# Install OpenCV for Blender's Python (if using 4.2.4)
./blender-4.2.4-linux-x64/4.2/python/bin/python3.11 -m pip install opencv-python

# Install OpenCV for Blender's Python (if using 4.2.9)
./blender-4.2.9-linux-x64/4.2/python/bin/python3.11 -m pip install opencv-python
```

## 💡Inference

### Troubleshooting

If you encounter GLTF import errors:
1. **Check Blender version**: Ensure you have either Blender 4.2.4 or 4.2.9 installed
2. **Verify OpenCV**: Make sure OpenCV is installed for Blender's Python
3. **Check mesh validity**: Ensure your input GLB files don't contain NaN values
4. **Log level errors**: The scripts now automatically handle log level issues with fallbacks

If you encounter NaN conversion errors:
1. **Mesh validation**: Check that your input mesh has valid UV coordinates
2. **Texture resolution**: Ensure texture dimensions are reasonable (512x512 or 1024x1024)
3. **Input images**: Verify input images are valid and don't contain problematic values

### Generate multi-view images

1. Prepare the input images and glbs in `/data/test/imgs` and `/data/test/glbs`.

2. Run the following command to infer:
    ```
    python infer_multiview.py --input_glb_dir ./data/test/glbs --input_img_dir ./data/test/imgs --output_dir ./outputs/test --geo_rotation 0
    ```

    **Mesh rotation settings:**
    - Mesh generated from [TripoSG](https://github.com/VAST-AI-Research/TripoSG), use `--geo_rotation 0`
    - Mesh generated from [Hunyuan-2](https://github.com/Tencent/Hunyuan3D-2), use `--geo_rotation -90`
    - Mesh generated from [TRELLIS](https://github.com/microsoft/TRELLIS), use `--geo_rotation -90`
    - Mesh generated from [Hi3dGen](https://github.com/Stable-X/Hi3DGen), use `--geo_rotation -90`

### Extract PBR (Optional)
Extract PBR attributes from generated mul-view images.

```
python infer_pbr.py --mv_res_dir ./outputs/test
```

The extracted PBR maps will be saved in `--mv_res_dir` directory.

### Painting

Run the following command to paint 3d model with multi-view generations:

```
python infer_paint.py --mv_res_dir ./outputs/test/mvpainter --output_dir ./results/test/mvpainter
```
If use PBR:
```
python infer_paint.py --mv_res_dir ./outputs/test/mvpainter --output_dir ./results/test/mvpainter --use_pbr
```

`--mv_res_dir` is the directory of the generated multi-view images.

## 🏋️Training
### Multi-View Generation Model
#### Train UNet
```
python train.py --base=./configs/mvpainter-train-unet.yaml --gpus 0 --num_nodes 32 --logdir=./logs
```

#### Train controlnet
```
python train.py --base=./configs/mvpainter-train-controlnet.yaml --gpus 0 --num_nodes 32 --logdir=./logs
```

### PBR Model
Our pbr model is improved based on [IDArb](https://github.com/Lizb6626/IDArb). The specific improvements can be found in our technical report. Our training script is similar to IDArb:

```
accelerate launch --config_file configs/acc/8gpu.yaml train_pbr.py --config configs/train.yaml

```
## 📊[Data Processing](./data_process/README.md)
Dataprocess guides can be found in [here](./data_process/README.md)

## 📝[Evaluation](./MVPainter/evaluation/README.md)
Our human-aligned evaluation system can be found in [here](./MVPainter/evaluation/README.md)

## Citation
If you found this repository helpful, please cite our report:

```
@article{shao2025mvpainter,
  title={MVPainter: Accurate and Detailed 3D Texture Generation via Multi-View Diffusion with Geometric Control},
  author={Shao, Mingqi and Xiong, Feng and Sun, Zhaoxu and Xu, Mu},
  journal={arXiv preprint arXiv:2505.12635},
  year={2025},
  url={https://arxiv.org/abs/2505.12635}
}
```
## ⭐️Acknowledgment

In this project we use parts of the implementations of the following works:
- [Hunyuan3D-1](https://github.com/Tencent/Hunyuan3D-1)
- [Hunyuan3D-2](https://github.com/Tencent/Hunyuan3D-2)
- [IDArb](https://github.com/Lizb6626/IDArb)
- [MaterialAnything](https://github.com/3DTopia/MaterialAnything)


We thank these work's contributors for open sourcing their research and exploration.

<!-- ## Star History

[![Star History](https://api.star-history.com/svg?repos=amap-cvlab/MV-Painter&type=Date)](https://www.star-history.com/#amap-cvlab/MV-Painter&Date) -->
