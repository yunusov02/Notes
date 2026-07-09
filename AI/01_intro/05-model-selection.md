
Model selection is the process of choosing the machine learning model that performs best for your problem


To avoid **Multiple Comparison Problem**

We split our features

```bash
Dataset
   │
   ▼
Split
   │
   ├── Train
   │      │
   │      ▼
   │   Train many models
   │
   ├── Validation
   │      │
   │      ▼
   │ Compare models
   │ Tune hyperparameters
   │ Select best model
   │
   └── Test
          │
          ▼
Final unbiased evaluation
```


Train set - lets the model learn
Validation set - lets you select models and tune hyperparameters without touching the test set
Test set - provides an unbiased estimate of how the final model will perform on new unseen data.
