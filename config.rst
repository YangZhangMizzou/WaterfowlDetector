
Configuration
=============================

Some basic training configurations are saved in :file:`/configs/Base-RCNN-FPN.yaml`. For more details you can check detectron2's `git repository <https://github.com/facebookresearch/detectron2/blob/main/detectron2/config/defaults.py>`_. 

.. code-block:: python
	:linenos:
	:emphasize-lines: 3,5

	MODEL:
	  META_ARCHITECTURE: "GeneralizedRCNN"
	  BACKBONE:
	    NAME: "build_resnet_fpn_backbone"
	  RESNETS:
	    OUT_FEATURES: ["res2", "res3", "res4", "res5"]
	  FPN:
	    IN_FEATURES: ["res2", "res3", "res4", "res5"]
	  ANCHOR_GENERATOR:
	    SIZES: [[32], [64], [128], [256], [512]]  # One size for each in feature map
	    ASPECT_RATIOS: [[0.5, 1.0, 2.0]]  # Three aspect ratios (same for all in feature maps)
	  RPN:
	    IN_FEATURES: ["p2", "p3", "p4", "p5", "p6"]
	    PRE_NMS_TOPK_TRAIN: 2000  # Per FPN level
	    PRE_NMS_TOPK_TEST: 1000  # Per FPN level
	    # Detectron1 uses 2000 proposals per-batch,
	    # (See "modeling/rpn/rpn_outputs.py" for details of this legacy issue)
	    # which is approximately 1000 proposals per-image since the default batch size for FPN is 2.
	    POST_NMS_TOPK_TRAIN: 1000
	    POST_NMS_TOPK_TEST: 1000
	  ROI_HEADS:
	    NAME: "StandardROIHeads"
	    IN_FEATURES: ["p2", "p3", "p4", "p5"]
	  ROI_BOX_HEAD:
	    NAME: "FastRCNNConvFCHead"
	    NUM_FC: 2
	    POOLER_RESOLUTION: 7
	  ROI_MASK_HEAD:
	    NAME: "MaskRCNNConvUpsampleHead"
	    NUM_CONV: 4
	    POOLER_RESOLUTION: 14
	DATASETS:
	  TRAIN: ("coco_2017_train",)
	  TEST: ("coco_2017_val",)
	SOLVER:
	  IMS_PER_BATCH: 16
	  BASE_LR: 0.02
	  STEPS: (60000, 80000)
	  MAX_ITER: 90000
	INPUT:
	  MIN_SIZE_TRAIN: (640, 672, 704, 736, 768, 800)
	VERSION: 2

Training
-------------------------------

You can also change training configuration in :file:`train_bird_faster_fpn.py`. all of the configuration in :file:`train_bird_faster_fpn.py` will overwrite configuration in :file:`/configs/Base-RCNN-FPN.yaml`

.. code-block:: python
	:linenos:
	:emphasize-lines: 3,5

	def make_trainer(image_dir,model_name,image_num,lr):
	    register_coco_instances("bird_dataset", {}, image_dir+"/tree.json", image_dir)
	    birds_metadata = MetadataCatalog.get("train")
	    birds_metadata.thing_classes = ['bird']
	    cfg = get_cfg()
	    cfg.merge_from_file("./configs/COCO-Detection/faster_rcnn_R_50_FPN_1x.yaml")
	    cfg.OUTPUT_DIR = './models/'+model_name
	    cfg.DATASETS.TRAIN = ("bird_dataset",)
	    cfg.DATASETS.TEST = ()
	    cfg.DATALOADER.FILTER_EMPTY_ANNOTATIONS = False
	    cfg.SOLVER.WARMUP_ITERS = 100
	    cfg.DATALOADER.NUM_WORKERS = 4
	    cfg.SOLVER.IMS_PER_BATCH = 2
	    cfg.MODEL.ROI_HEADS.BATCH_SIZE_PER_IMAGE = 512
	    cfg.SOLVER.BASE_LR = lr
	    cfg.SOLVER.STEPS = (30000,45000)
	    cfg.SOLVER.GAMMA = 0.1
	    cfg.MODEL.ROI_HEADS.SCORE_THRESH_TEST = 0.05
	    cfg.SOLVER.MAX_ITER = 60000
	    cfg.MODEL.ROI_HEADS.NUM_CLASSES = 1
	    os.makedirs(cfg.OUTPUT_DIR, exist_ok=True)
	    trainer = Trainer(cfg) 
	    trainer.resume_or_load(resume=False)
	    return trainer

Inference
-------------------------------
There are also some configurations in :file:`image_inference_bird.py` 

.. code-block:: python
	:linenos:
	:emphasize-lines: 3,5

	cfg = get_cfg()
	cfg.merge_from_file('./pretrained_weight/COCO-Detection/faster_rcnn_R_50_FPN_1x.yaml')
	cfg.MODEL.ANCHOR_GENERATOR.SIZES = [[8], [16], [32],[64],[128]]
	cfg.OUTPUT_DIR = model_name
	cfg.MODEL.ROI_HEADS.NUM_CLASSES = 1  # only has one class (ballon)
	cfg.MODEL.WEIGHTS = os.path.join(cfg.OUTPUT_DIR, "model_final.pth")
	cfg.MODEL.ROI_HEADS.SCORE_THRESH_TEST = 0.1   # set the testing threshold for this model
	predictor = DefaultPredictor(cfg)