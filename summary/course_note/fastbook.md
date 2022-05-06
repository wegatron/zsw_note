## ML Basic Concept
* `train`, `valid`, `test` data set & split stragety
* loss function
    * softmax/sigmoid
        $$
            \mathrm{sigmoid}(x) = \frac{1}{1+e^{-x}}
        $$
    * nll_loss (negative log likelihood loss)
        $$
            \mathrm{Negative \; Log \; Likelihood \; Loss}(x, targ) = -\ln(x[targ])\\
        $$
        in python:
        $$
            \mathrm{nll\_loss}(x, trag) = -[x, trag]
        $$
    * CrossEntropyLoss
        put all together, single classification problem:
        $$
            \mathrm{CrossEntropyLoss} = \sum -\ln(\mathrm{sigmoid}(x[trag]))
        $$
    * BCEWithLogitLoss
        
    * MSELoss
* error metric
    * accuracy
    * recall
* learning rate
* transform learning
* training details
    * learning rate

* basic data process
    * batch_tfms
    * aug_transforms

* normalization

## Coding Basic

* fastbook init
    ```python
    !pip install -Uqq fastbook
    import fastbook
    fastbook.setup_book()

    from fastbook import *
    from fastai.vision.all import *
    ```

* DataSet & DataLoader
    view data before training
    ```python
    dls = dblock.dataloaders(path)
    dls.show_batch(max_n=9, figsize=(8,6))
    
    # view tensor shape
    xb, yb=dls.one_batch()
    xb.shape, yb.shape
    ```

## python 语法
```python
biwi = DataBlock(
    blocks=(ImageBlock, PointBlock),
    get_items=get_image_files,
    get_y=get_ctr,
    splitter=FuncSplitter(lambda o: o.parent.name=='13'),
    batch_tfms=[*aug_transforms(size=(240,320)), # 这里*代表什么意思
                Normalize.from_stats(*imagenet_stats)]
)
```