
# Unsupervised Domain Expansion 

## Introduction

It has been recognized early that visual classifiers trained on a specific domain do not necessarily perform well on a distinct domain. Expanding visual categorization into a novel domain without the need of extra annotation has been a long-term interest for multimedia intelligence. Previously, this challenge has been approached by unsupervised domain adaptation (UDA). While UDA focuses on the target domain, we argue that the performance on both source and target domains matters, as in practice which domain a test example comes from is unknown. Moreover, how domain-adapted models perform in the original
source domain is mostly unreported. The absence of performance evaluation on the source domain raises two important questions: 
+ Is a domain-adapted model indeed domain-invariant? 
+ Is the performance gain for the target domain obtained at the cost of significant performance loss in the source domain? 

We introduce a new task called *Unsupervised Domain Expansion* (UDE), aiming to adapt a deep model for the target domain with its unlabeled data, meanwhile maintaining the model’s performance on the source domain. 


## Data for UDE

+ **tomm2021ude-data** (34M). We built this dataset for UDE by re-purposing two public datasets, [Office-Home](https://www.hemanthdv.org/officeHomeDataset.html) and [DomainNet](http://ai.bu.edu/M3SDA/), originally developed for domain adaptation. Different from the setting of domain adapation which uses all examples in the source domain for training, we have divided the source-domain examples into two disjoint parts, *training* and *test*, so the performance of domain-adapted or domain-expanded models on the original source domain can be evaluated. Download links: [google drive](https://drive.google.com/file/d/1dOHy5aoSl7oUd04EAuHm0uLb-xvnneNT/view?usp=sharing), [百度云盘-提取码: xyfh](https://pan.baidu.com/s/10PA2djBTN9EZjgyQbd7xCQ)

| Dataset          | Classes | Images  | Domains (images)                                                     |
|------------------|---------:|---------:|----------------------------------------------------------------------|
| officehome_train |      65 |   7,728 | Art (1,201), Clipart (2,165), Product (2,201), Real_World (2,161)    |
| officehome_test  |      65 |   7,860 | Art (1,226), Clipart (2,200), Product (2,238), Real_World (2,196)    |
| domainnet_train  |     345 | 253,059 | clipart (33,525), painting (50,416), real (120,906), sketch (48,212) |
| domainnet_test   |     345 | 109,411 | clipart (14,604), painting (21,850), real (52,041), sketch (20,916)  |

We suggest readers to use this data split so the models reported below can be directly and fairly compared.

## Performance



### Models

+ ResNet50: Trained exclusively on the source domain. 
+ DDC: A classical deep domain adaptation model that minimizes domain discrepancy measured in light of first-order statistics of the deep features (Tzeng *et al*., Deep Domain Confusion: Maximizing for Domain Invariance, ArXiv 2014)
* CDAN: Domain adaptation by adversarial learning, using multilinear conditioning of deep features and classification results as the input of its discriminator (Long *et al*., Conditional Adversarial Domain Adaptation, NeurIPS 2018)
+ KDDE: Our proposed method (Wang *et al*., Unsupervised Domain Expansion for Visual Categorization, TOMM 2021)

### Office-Home

```bash
python eval_all_tasks.py --test_collection officehome_test
```

| Model      | Source domains | Target domains | Expanded domains |
|------------|---------------:|---------------:|-----------------:|
| ResNet50   |         82.44 |         56.85 |           69.64 |
| DDC        |         82.20 |         60.34 |           71.27 |
| CDAN       |         80.24 |         61.43 |           70.83 |
| KDDE(DDC)  |         **82.57** |         61.62 |           **72.10** |
| KDDE(CDAN) |         80.85 |         **62.57** |           71.71 |


### DomainNet

```bash
python eval_all_tasks.py --test_collection domainnet_test
```


| Model      | Source domains | Target domains | Expanded domains |
|------------|---------------:|---------------:|-----------------:|
| ResNet50   |         **74.59** |         41.49 |           58.04 |
| DDC        |         72.44 |         46.20 |           59.32 |
| CDAN       |         69.73 |         45.21 |           57.47 |
| KDDE(DDC)  |         73.77 |         **48.04** |           **60.91** |
| KDDE(CDAN) |         72.98 |         47.65 |           60.32 |


Evaluation regarding a specific UDE task is also provided, 
```bash
python eval_per_task.py --test_collection domainnet_test --source_domain real --target_domain clipart

#Performance of the real->clipart UDE task on domainnet_test
#model source-domain target-domain expanded-domain
ResNet50_real 82.96 49.60 66.28
DDC_ResNet50_real_clipart 81.16 50.08 65.62
CDAN_ResNet50_real_clipart 79.10 50.99 65.05
KDDE_DDC_ResNet50_real_clipart 82.19 52.68 67.44
KDDE_CDAN_ResNet50_real_clipart 81.37 53.56 67.47
```



## Publications on UDE


Citation of the UDE task and data is the following:

```
@article{tomm-ude,      
title={Unsupervised Domain Expansion for Visual Categorization},    
author={Jie Wang and Kaibin Tian and Dayong Ding and Gang Yang and Xirong Li},     
journal = {ACM Transactions on Multimedia Computing Communications and Applications (TOMM)},   
year={2021},  
note={in press},  
}
```

