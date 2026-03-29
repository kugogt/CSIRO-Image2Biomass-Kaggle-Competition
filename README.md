# 🌱 CSIRO - Image2Biomass Prediction. Model Ensamble with ConvNeXt

Ciao! Welcome into my Kaggle competition result. This was my first competition that I took! The task is use pasture images to predict five key biomass components critical for grazing and feed management:
- Dry green vegetation;
- Dry dead material
- Dry clover biomass
- Green dry matter (GDM)
- Total dry biomass

You can find the dataset and everything related at this competition here: [CSIRO - Image2Biomass Prediction](https://www.kaggle.com/competitions/csiro-biomass).

---

## Model Architecture

### Backbone
ConvNeXt-Small (pretrained weights, fully fine-tuned)

### Feature processing
- Squeeze-and-Excitation Attention Block
- GeM Pooling (Generalized Mean Pooling)

### Prediction Head
- Multi-output regression (shared representation + separate heads)

---

## Training Setting
- Image size: 256×256
- Augmentations: flips, rotations, MixUp
- Loss: Huber
- Optimizer: AdamW with cosine decay
- Hyperparameters tuned with Optuna

## Cross-Validation Strateg
- Type: 5-Fold Cross-Validation
- Ensembling: Final predictions are a weighted average of the best models from each fold.
- Two-Stage Training: A "Warm-up" phase training only the regression heads, followed by a full backbone fine-tuning with a significantly lower learning rate.

---

## Hyperparameter Optimization (Optuna)

A full Optuna study was performed to tune:

### Architecture
- Dense units (shared, pre-head, head)
- Attention ratio
- GeM pooling parameter

### Regularization
- Dropout rates
- Noise level
- MixUp alpha

### Optimization
- Learning rate
- Weight decay
- Decay steps
- Huber delta

### Strategy
- A 7 different step of 13 trials each
- Median pruning
- Integrated logging with Weights & Biases

---

## Inference & Results 
### Test-Time Augmentation (TTA) applied:
1. Horizontal /vertical flips
2. Rotations
- Final predicion obtained by averaging multiple aumented predictions

### Physics-Informed Post-Processing
Instead of trusting raw model outputs, a hierarchy was enforced:
- GDM \leq Total
- Green \leq GDm
- Derived values (Dead and Clover) were calculated from the cleaned primary predictions to ensure logical consistency.

Prediction on the test sample image:

| Target Name | Predicted Value (g) |
| :--- | :--- |
| Dry Green Biomass | 0.000000 |
| Dry Dead Material | 21.953606 |
| Dry Clover Biomass | 33.913876 |
| GDM (Green Dry Matter) | 55.867481 |
| Total Dry Biomass | 33.913876 |

---

## Citation

@misc{csiro-biomass,
    author = {Qiyu Liao and Dadong Wang and Rhys Pirie and Joshua Whelan and Rebecca Haling and Jiajun Liu and Rizwan Khokher and Xun Li and Martyna Plomecka and Addison Howard},
    title = {CSIRO - Image2Biomass Prediction},
    year = {2025},
    howpublished = {\url{https://kaggle.com/competitions/csiro-biomass}},
    note = {Kaggle}
}






