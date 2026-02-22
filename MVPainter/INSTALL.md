# Prepare 
To run MVPainter you need at least 40GB GPU memory.

Following instructions are tested on Ubuntu 22.04 with CUDA 12.1 and Python 3.10.
1. Create conda env:
    ```
    conda create -n mvpainter python==3.10
    conda activate mvpainter
    pip3 install torch torchvision --index-url https://download.pytorch.org/whl/cu121
    pip install -r requirements.txt
    ```


2. Install custom rasterizer and differentiable renderer:
    ```
    cd mvpainter/custom_rasterizer
    python setup.py install
    cd ../..
    cd mvpainter/differentiable_renderer
    python setup.py install

    ```

3. Install cupy:
    ```
    conda install -c conda-forge cupy
    ```

4. Install blender and opencv-python for blender's python

    Download blender
    ```
    cd ../../..
    wget https://download.blender.org/release/Blender4.2/blender-4.2.4-linux-x64.tar.xz
    tar -xvf blender-4.2.4-linux-x64.tar.xz
    ```
    Install opencv for blender
    ```
    ./blender-4.2.4-linux-x64/4.2/python/bin/python3.11 -m pip install opencv-python
    ```
