# Automatic building of knowledge graphs from Russian texts
This repo is made to supplement the research described in a Master's Thesis by Aidinian Artem on a topic of "Building Knowledge Graphs from Texts".

## Prepare the repo
1) Download mbart-large-50 model into root of the repo.
2) Download REBEL-RU dataset (see below) in root/data/rebel/ru.jsonl
3) Generate train-val-test files with training_rebel-ru.ipynb which is in the root. The notebook will also output relations_count_ru.tsv.
4) Place newly generated files according to the dataset naming in conf file. By default in root/data/rebel

The repo is now ready for training the model.

## Generate knowledge graph
We provide *hse-ru.ipynb* - a notebook that draws Knowledge Graph from Wikipedia article.
The notebook uses scripts from *utils* folder. Make sure to have it with the notebook.

An example in the notebook illustrates a building of star-like knowledge graph from a Russian Intro to *Higher School of Economics* Wikipedia article.

## Constructed Knowledge Graphs
Demonstration of KGs is located in *demo* folder. It consists of images from Intros of 4 Wikipedia articles. Larger amounts of texts create bigger graphs with a structure that is hard to display without a specified software. 

## Model
Model underneath is a modified REBEL. We replace BART model with MBart-large-50 with additional changes to token handling and data. 
To recreate the model we provide a command that runs a train.py script with our configurations for REBEL-RU dataset.

To train and test the model on RURED it is possible to use TACRED template since the dataset is delivered in the same format. Transformations is [here](https://github.com/InstituteForIndustrialEconomics/rured/blob/master/convert_brat_to_tacred.py)

Training procedure is launched as follows:
```
python train.py data=rebelru_data train=rebelru_train model=rebelru_model
```

## Pretraining
REBEL-RU was pretrained on 98.9% of a silver dataset REBEL-RU that we created and it is available [here](https://huggingface.co/datasets/InfroLab/REBEL-RU). Tested on 5.5% of REBEL-RU.
The model was trained on 1xNVIDIA Tesla V100 GPU?? 512GB RAM and 80xIntel CPUs. We express great appreciation to our employer [SBER](https://sber.ru/) for giving an access to the hardware.

Model was also trained and tested in RURED dataset available [here](https://github.com/InstituteForIndustrialEconomics/rured). RURED is made by RANEPA researchers from Lenta.ru Economical articles.

## Performance
We evaluate REBEL-RU pretrained model on REBEL-RU and RURED. Our table below shows the F1, Precision and Recall for Relation Extraction task.
For RURED dataset, our result beats the scores described in [paper](https://www.dialog-21.ru/media/5093/gordeevdiplusetal-031.pdf). Improvements in scores are added into paretheses in the table below:

| Model + Dataset        | F1    | Precision | Recall |
|-----------------|-------|-----------|--------|
| REBEL_RU_pretrained + REBEL-RU_silver(ours)| 0.865 | 0.891     | 0.84   |
| REBEL_RU_pretrained + RURED(main score, ours)           | **0.821**(+3.9%) | **0.863**(+2.2%)    | **0.783**(+5.2%)  |
| SpanBERT + RURED(Gordeev et al.)           | 0.782 | 0.841     | 0.731  |

The performance measurements are made on only two datasets, which is a consequence of low number of large and quality Russian datasets available for Relation Extraction task.

Our approach gives a 4% improvement in F1-score which is essential change, especially when we deal with larger text datasets that we use to extract relations from.
It is also worth mentioning that pretraining strategy works well with REBEL-RU. Given that REBEL-RU silver dataset is a general domain dataset and RURED is an economical domain dataset our results are quite significant.
