
Installing WaterfowlDetector
=============================


Requirements
---------------------------

#. Linux or windows
#. Recommend installing cuda v11.3 and pytorch 1.10.0
#. Recommend running on a gpu with compatible cuda
#. python >= 3.7

Required Python Packages
---------------------------
You can install our WaterfowlDetector platform on your Ubuntu OS. Firstly, you need to install the following python packages with pip3::

  pip3 install numpy==1.23.3
  pip3 install pandas
  pip3 install cv2
  pip3 install tqdm
  pip3 install Detectron2

Detectron2 is Facebook AI Reseach's next generation library that provide state-of-art detection and segmentation algorithms. You can refer to their git repository when you install Detectron2. See `Detectron2 <https://github.com/facebookresearch/detectron2/>`_

Source Installation
---------------------------

All of our scripts and sample data are uploaded to our `git repository <https://github.com/YangZhangMizzou/Bird-Detectron2.git>`_. You can easily download and unzip it yourself or install by following code::

  git clone https://github.com/YangZhangMizzou/Bird-Detectron2.git
  cd Bird-Detectron2

