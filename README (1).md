
# Table Detection and OCR with LayoutParser

This repository contains code for detecting tables in images and performing Optical Character Recognition (OCR) on the detected tables using the [LayoutParser](https://github.com/Layout-Parser/layout-parser) library. 


## Installation

Install my-project with npm

```bash
  To run the code in this repository, you'll need to install the required dependencies:

```bash
!pip install -U layoutparser
!pip install 'git+https://github.com/facebookresearch/detectron2.git@v0.4#egg=detectron2'
!pip install layoutparser[ocr]
```
Additionally, you need to clone the LayoutParser repository and navigate to the appropriate directory:
    
## Getting Started
Load and Preprocess the Image:
Load an image using OpenCV and convert it to the required format.

Initialize the Layout Model:
Initialize the Detectron2LayoutModel with a pre-trained model and label mapping.
```bash
import layoutparser as lp
model = lp.Detectron2LayoutModel('lp://PubLayNet/mask_rcnn_X_101_32x8d_FPN_3x/config', extra_config=["MODEL.ROI_HEADS.SCORE_THRESH_TEST", 0.65],
                                 label_map={0: "Text", 1: "Title", 2: "List", 3: "Table", 4: "Figure"})
layout = model.detect(image)
```
Filter Text Blocks:
Separate text blocks from other elements in the layout.
```bash
text_blocks = lp.Layout([b for b in layout if b.type == "Table"])
```
Perform OCR:
Initialize the Tesseract OCR agent and extract text from each table segment.
```bash
ocr_agent = lp.TesseractAgent(languages='eng')
for block in text_blocks:
    segment_image = (block.pad(left=5, right=5, top=5, bottom=5).crop_image(image))
    text = ocr_agent.detect(segment_image)
    block.set(text=text, inplace=True)
    ```

## Dependencies
LayoutParser: A Python library for document layout analysis.
Detectron2: A high-performance, modular object detection library.
Tesseract OCR: Install using sudo apt install tesseract-ocr