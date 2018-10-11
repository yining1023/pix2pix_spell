# pix2pix
Use Spell to train a pix2pix model, run the model in tensorflow.js and ml5.js

## Demo:
Edges2Pikachu: [https://yining1023.github.io/pix2pix_spell/edges2pikachu/](https://yining1023.github.io/pix2pix_spell/edges2pikachu/)

## Training a Edges2Pikachu model: see instructions [https://github.com/yining1023/pix2pix_tensorflowjs_lite](https://github.com/yining1023/pix2pix_tensorflowjs_lite)

## Training a Label2Facades model with [Spell](http://spell.run)

```
# make sure you have Tensorflow 0.12.1 installed first
python -c "import tensorflow; print(tensorflow.__version__)"

# clone the repo
git clone https://github.com/affinelayer/pix2pix-tensorflow.git
cd pix2pix-tensorflow

# download the CMP Facades dataset http://cmp.felk.cvut.cz/~tylecr1/facade/
python tools/download-dataset.py facades

# Let spell know the newly downloaded dataset
git add .
git commit -m “added facade images”

# train the model
# this may take 1-9 hours depending on GPU, on CPU you will be waiting for a bit
# You could choose V100 or K80 as machine type
spell run --machine-type V100 \
            --framework tensorflow \
  "python pix2pix.py \
  --mode train \
  --output_dir facades_train \
  --max_epochs 200 \
  --input_dir facades/train \
  --which_direction BtoA"

# After the run is finished (1h, 8m, 16s)
# check out the result 
spell ls runs/YOUR_RUN_NUMBER
spell ls runs/YOUR_RUN_NUMBER/facades_train

# Go to the pix2pix-tensorflow repo
cd pix2pix-tensorflow/
mkdir facades_train
cd facades_train

# Copy the result folder,  takes ~15mins
spell cp runs/YOUR_RUN_NUMBER/facades_train

# test the model
python pix2pix.py \
  --mode test \
  --output_dir facades_test \
  --input_dir facades/val \
  --checkpoint facades_train
# After testing, you should be able to see output images in the facades_test folder

# export the model
python pix2pix.py --mode export --output_dir export/ --checkpoint facades_train/ --which_direction BtoA
# It will create a new export folder

# Port the model to tensorflow.js (python 2 doesn’t have tempfile, so use python3 instead)
cd server
cd static
mkdir models
cd ..
python3 tools/export-checkpoint.py --checkpoint ../export --output_file static/models/facades_BtoA.pict
# We should be able to get a file named facades_BtoA.pict, which is 13.6 MB.

# Copy the model we got to the `models` folder.

```
