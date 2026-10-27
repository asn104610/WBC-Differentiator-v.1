# WBC-Differentiator-v.1

\# WBC Differentiator



A beginner-friendly deep learning project that classifies microscope images of peripheral blood cells into 8 types. Built as a learning project by a Medical Laboratory Science student. \*\*\[Add your name here if you like.]\*\*



> \*\*For education only.\*\* This is not a diagnostic tool and has not been validated for clinical use.



\## What it does



Given a single cell image, the model predicts one of 8 classes: neutrophil, eosinophil, basophil, lymphocyte, monocyte, immature granulocyte (ig), erythroblast, or platelet.



\## Dataset



\- \*\*Source:\*\* Acevedo et al., "A dataset of microscopic peripheral blood cell images for development of automatic recognition systems," \*Data in Brief\*, 2020 (hosted on Mendeley Data).

\- \*\*Size:\*\* 17,092 JPG images, about 360 x 363 pixels, 8 classes, annotated by expert clinical pathologists.

\- \*\*Class counts:\*\* neutrophil 3,329 / eosinophil 3,117 / ig 2,895 / platelet 2,348 / erythroblast 1,551 / monocyte 1,420 / basophil 1,218 / lymphocyte 1,214.

\- The images are \*\*not\*\* stored in this repo. Download them from the Mendeley Data page and check the license and citation terms there.



\## Method



\- \*\*Split:\*\* 70 / 15 / 15 train / validation / test, stratified by class so each split keeps the same cell-type mix (11,964 / 2,564 / 2,564 images, `random\_state=42`). The exact split is saved in `train.csv`, `val.csv` and `test.csv`.

\- \*\*Model:\*\* ResNet18 pretrained on ImageNet (transfer learning), with the final layer replaced by an 8-class layer. Images are resized to 224 x 224.

\- \*\*Training:\*\* Google Colab (free T4 GPU), Adam optimizer, learning rate 1e-4, batch size 64.

\- \*\*v1:\*\* 3 epochs, no augmentation.

\- \*\*v2:\*\* 6 epochs, random flips and small brightness, contrast and saturation changes. The best epoch was chosen using the validation set only.

\- The test set was used \*\*once per model\*\*, after the model was chosen.



\## Results



| Model | Validation accuracy | Test accuracy |

|---|---|---|

| v1 | 0.981 | 0.984 |

| v2 | 0.987 | 0.986 |



The overall difference between v1 and v2 is small, about 5 images out of 2,564, and is within the range of normal variation.



F1 score per class on the test set:



| Class | v1 | v2 |

|---|---|---|

| basophil | 0.981 | 0.992 |

| eosinophil | 1.000 | 0.999 |

| erythroblast | 0.983 | 0.989 |

| ig | 0.966 | 0.971 |

| lymphocyte | 0.981 | 0.981 |

| monocyte | 0.986 | 0.979 |

| neutrophil | 0.977 | 0.980 |

| platelet | 0.997 | 0.997 |



\## What the errors look like



Most mistakes involve one pair: \*\*neutrophils and immature granulocytes (ig)\*\*. As I understand the dataset, the neutrophil class includes band forms and the ig class includes metamyelocytes, myelocytes and promyelocytes, so the confusion sits on the band / metamyelocyte border, a call that is subjective even between trained people. Reviewing the mistakes by eye, roughly half looked like reasonable calls a human could also make.



v2 shifted that decision line: ig recall rose from 0.959 to 0.982, while ig precision fell from 0.974 to 0.962 and neutrophil recall fell from 0.976 to 0.968. In other words, v2 flags more borderline cells as immature.



\## Limitations



\- All images come from one source (one lab, one scanner, one staining routine). Performance on slides from elsewhere is unknown and is likely lower.

\- The split was random by image. If near-identical images from the same slide landed in both train and test, scores may be slightly optimistic.

\- The model must pick one of 8 labels. It has no "none of the above" option and will give a confident answer even for a non-cell image.

\- Only 8 cell types are covered. Abnormal cells, artifacts and other classes are not.



\## Repo contents



\- `01\_setup\_check.ipynb`: environment check

\- `01\_explore\_data.ipynb`: class counts, sample images, image sizes

\- `02\_split\_data.ipynb`: stratified split and image loader

\- `03\_train\_resnet18.ipynb`: first training run (v1)

\- `04\_review\_and\_test.ipynb`: error review, v2 training, test results

\- `train.csv`, `val.csv`, `test.csv`: the exact data split

\- `requirements.txt`: Python packages for the local notebooks



Model weights are kept outside the repo in Google Drive.



\## How to reproduce



1\. Download the dataset from the Mendeley Data page and unzip it into a `Data` folder.

2\. Create a virtual environment and install `requirements.txt`.

3\. Run notebooks 01 and 02 locally to explore and split the data.

4\. Run notebooks 03 and 04 in Google Colab with a GPU (the paths in the CSVs are rewritten there).



\## Citation



Please cite the original dataset paper (Acevedo et al., \*Data in Brief\*, 2020) if you use the data.

