
Getting started
=============================
WaterfowlDetector platform mainly consists of two parts, training and inference.

Training
-------------------------------

Data preparation
++++++++++++++++++++++

Although we provide pretrained model of our six datasets, we also support customized training on your own bird images. If you want to train you custom datasets, you should prepare your dataset in `COCO <https://cocodataset.org/#format-data format>`_  format.

Or, if your high resolution images are labeled by `labelme <https://github.com/wkentaro/labelme>`_, you should have json files which stroe annotation information for each image. In that case provide script can make your dataset prepared for training.
You can run the following line to prepare your training dataset::

  cd images
  python data_preparation.py -p [path to your image folder]

After that, you should have all of your high resolution images cropped and saved in a folder called :file:`all_small`. In that folder you will find all of cropped images and annotation json files. There is a json file :file:`bird_real.json` which include all annotation information for images containing birds. cropped images without birds will not include in :file:`bird_real.json`.


Training start
++++++++++++++++++++++
After you finish data preparation and configuration, you can start training with one command::

  python train_bird_faster_fpn.py -p [path to your image folder] -m [model name you decide]

The trained model will be saved in :file:`/models/model_name`. Training weight will be saved in :file:`model_final.pth`


Inference and evaluation
-------------------------------
Our scripts support inference image with any size. The model infers all low-resolution images and projects all inference results back to high-resolution images.

Inference with ground truth label
+++++++++++++++++++++++++++++++++++++++++++++

If you have ground truth annoation file :file:`bird.json` in your :file:`all_small` folder, you can do the inference with::

  python image_inference_bird.py -p [path to your image folder] -m [model name] -t [confidence threshold]

Instead of MAP and MAR, we use precision, recall and f1-score to evaluate our models performance at the threshold you set. Both inferred images and evaluation results will be saved in your image folder.

Inference without ground truth label
+++++++++++++++++++++++++++++++++++++++++++++
You can also inference your images although no ground truth labels provided. You can use same command but no evaluation will be generated.

.. image:: _static/Cloud_Ice_60m_test.JPG
   :align: center
