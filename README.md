# pix2pix
Use Spell to train a pix2pix model, run the model in tensorflow.js and ml5.js

## Demo:
Edges2Pikachu: [https://yining1023.github.io/pix2pix_spell/edges2Pikachu](https://yining1023.github.io/pix2pix_spell/edges2Pikachu)

## Training the model with [Spell](http://spell.run)

```
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
spell run --machine-type V100 \
            --framework tensorflow \
  "python pix2pix.py \
  --mode train \
  --output_dir facades_train \
  --max_epochs 200 \
  --input_dir facades/train \
  --which_direction BtoA"

# test the model
python pix2pix.py \
  --mode test \
  --output_dir facades_test \
  --input_dir facades/val \
  --checkpoint facades_train
```
